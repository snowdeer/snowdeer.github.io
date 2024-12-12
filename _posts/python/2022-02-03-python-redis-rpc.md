---
layout: post
title: Python Redis RPC 예제
category: Python
tag: [Python]
---

# Pythong Redis RPC

## redis_rpc.py

<pre class="prettyprint">
import asyncio
import json
import uuid
import redis
import logging

logger = logging.getLogger('redis_rpc')


class TimeoutException(Exception):
    pass


class Server(object):
    def __init__(self, name, host='localhost', port=6379, timeout = None):
        self.__redis = redis.Redis(host=host, port=port)
        self.__prefix = 'pyredisrpc:'
        self.__queue = self.__prefix + name
        self.__response_expire_time = 60
        self.__methods = {}
        self.__is_running = False
        self.__timeout = timeout

    def start(self):
        logging.debug('RedisRpcServer::start()')
        self.__redis.flushall()

        self.__is_running = True
        while self.__is_running:
            _, req_data = self.__redis.blpop(self.__queue)
            req_data = req_data.decode()
            req_args = self.__parse_request(req_data)
            if req_args is None:
                continue

            req_id, method, params, timeout_check = req_args
            if timeout_check and self.__is_timeout_expired(req_id):
                continue

            self.__call_method(req_id, method, params)

    def shutdown(self):
        logging.debug('RedisRpcServer::shutdown()')
        self.__is_running = False

    def __parse_request(self, req_data):
        try:
            req = json.loads(req_data)
        except json.JSONDecodeError:
            logger.warning(f'Exception - invalid request(JSON Dcode error)\n{req_data}')
            return

        try:
            req_id = req['id']
            method = req['method']
            params = req['params']
        except KeyError as e:
            key = e.args[0]
            self.__send_response(req_id, None, f'Exception - invalid request key({key})')
            return

        if method not in self.__methods:
            self.__send_response(req_id, None, f'Exception - invalid method({method})')
            return

        if type(params) != list or len(params) != 2 or type(params[0]) != list or type(params[1]) != dict:
            self.__send_response(req_id, None, f'Exception - invalid params({params})')
            return

        timeout_check = req.get('timeout') == 1

        return req_id, method, params, timeout_check

    def __is_timeout_expired(self, req_id):
        timeout_key = self.__prefix + req_id + ':timeout'
        return self.__redis.get(timeout_key) is None

    def __call_method(self, req_id, method, params):
        func = self.__methods[method]
        params_args = params[0]
        params_kw = params[1]

        try:
            result = await asyncio.wait_for(
                fut=func(*params_args, **params_kw),
                timeout=self.__timeout,
            )
            logger.info(f'{method}() is requested. params: {params}, result: {result}')
            self.__send_response(req_id, result)
        except asyncio.TimeoutError as e:
            await self.__send_response(
                req_id, None, f'{type(e)} - Call execution timeout. Slow call is cancelled.',
            )
        except Exception as e:
            self.__send_response(req_id, None, f'Exception - {repr(e)}')

    def __send_response(self, req_id, result, error_message=None):
        if error_message is not None:
            logger.warning(f'Exception - {error_message}')

        result = {'id': req_id, 'result': result, 'error': error_message}

        key = self.__prefix + req_id
        self.__redis.rpush(key, json.dumps(result))
        self.__redis.expire(key, self.__response_expire_time)

    def method(self, f):
        self.__methods[f.__name__] = f


class Client(object):
    def __init__(self, name, host='localhost', port=6379, timeout=0):
        self.__redis = redis.Redis(host=host, port=port)
        self.__prefix = 'pyredisrpc:'
        self.__queue = self.__prefix + name
        self.__timeout = timeout

    def __call(self, method, params):
        req_id = uuid.uuid4().hex
        req = {'id': req_id, 'method': method, 'params': params}

        if self.__timeout != 0:
            req['timeout'] = 1
            timeout_key = self.__prefix + req_id + ':timeout'
            self.__redis.set(timeout_key, 1, self.__timeout)

        self.__redis.rpush(self.__queue, json.dumps(req))
        key = self.__prefix + req_id
        res = self.__redis.blpop(key, self.__timeout)

        if not res:
            raise TimeoutException(req, key)

        _, response_data = res
        response = json.loads(response_data.decode())

        if response['error'] is not None:
            raise Exception(response['error'])

        return response['result']

    def __getattr__(self, method):
        def wrap(*args, **kw):
            return self.__call(method, [args, kw])

        return wrap

</pre>

## server.py

<pre class="prettyprint">
import logging

import redis_rpc as pyredisrpc
from colored_log_handler import ColoredLogHandler

logging.basicConfig(level="DEBUG", handlers=[ColoredLogHandler()])
logger = logging.getLogger('main')

server = pyredisrpc.Server('snowdeer-redis-rpc-example')


@server.method
def add(a, b):
    return a + b


try:
    server.start()
except KeyboardInterrupt:
    print("Interrupted")

server.shutdown()

</pre>

## client.py

<pre class="prettyprint">
import logging
import time
import random

import redis_rpc as pyredisrpc
from colored_log_handler import ColoredLogHandler

logging.basicConfig(level="DEBUG", handlers=[ColoredLogHandler()])

client = pyredisrpc.Client('snowdeer-redis-rpc-example', timeout=1)

for i in range(0, 200):
    x = random.randint(1, 10)
    y = random.randint(1, 10)

    ret = client.add(x, y)

    if ret == (x + y):
        logging.info(f'# test({i}) - ({x}, {y} --> {ret})')
    else:
        logging.warning(f'# test({i}) - ({x}, {y} --> {ret})')

    time.sleep(0.5)

</pre>
