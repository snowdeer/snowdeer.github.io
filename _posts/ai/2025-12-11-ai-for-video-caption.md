---
layout: post
title: 영상 캡셔닝 AI 모델(ViT) 테스트
category: AI
permalink: /ai/:year/:month/:day/:title/

tag: [ai, model]
---

# 실행 환경

맥북(M1 Pro) 기반으로 실습 테스트

### 패키지 설치

<pre class="prettyprint">
# 가상환경 생성
python -m venv venv
source venv/bin/activate

# 패키지 설치
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/nightly/cpu
pip install transformers pillow opencv-python accelerate
</pre>

### 사용 가능 디바이스 확인

Apple Silicon 환경에서는 `mps` 디바이스 사용 가능

### ViT 기반 모델

영상에서 8개의 keyframe을 추출해서 캡션을 생성하는 코드

### main.py

<pre class="prettyprint">
import torch
import cv2
from PIL import Image
from transformers import VisionEncoderDecoderModel, ViTImageProcessor, GPT2Tokenizer
import argparse
import sys

def setup_device():
    """M1 Pro MPS 또는 CPU 설정"""
    if torch.backends.mps.is_available():
        return "mps"
    return "cpu"

def load_model(device):
    """모델과 토크나이저 로드"""
    model_name = "nlpconnect/vit-gpt2-image-captioning"
    model = VisionEncoderDecoderModel.from_pretrained(model_name)
    feature_extractor = ViTImageProcessor.from_pretrained(model_name)
    tokenizer = GPT2Tokenizer.from_pretrained(model_name)
    
    # 패딩 토큰 설정
    if tokenizer.pad_token is None:
        tokenizer.pad_token = tokenizer.eos_token
    
    model.to(device)
    model.eval()
    return model, feature_extractor, tokenizer

def extract_key_frames(video_path, max_frames=8):
    """주요 프레임 추출"""
    cap = cv2.VideoCapture(video_path)
    total_frames = int(cap.get(cv2.CAP_PROP_FRAME_COUNT))
    
    frames = []
    step = max(1, total_frames // max_frames)
    
    for i in range(0, total_frames, step):
        cap.set(cv2.CAP_PROP_POS_FRAMES, i)
        ret, frame = cap.read()
        if ret:
            rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)
            frames.append(Image.fromarray(rgb_frame))
    
    cap.release()
    return frames

def generate_captions(model, feature_extractor, tokenizer, frames, device):
    """각 프레임 캡션 생성"""
    captions = []
    
    for frame in frames:
        # 이미지 전처리
        pixel_values = feature_extractor(images=frame, return_tensors="pt").pixel_values.to(device)
        
        # 캡션 생성
        with torch.no_grad():
            generated_ids = model.generate(
                pixel_values, 
                max_length=30,
                num_beams=5,
                early_stopping=True,
                pad_token_id=tokenizer.eos_token_id
            )
        
        # 토크나이저로 디코딩
        caption = tokenizer.batch_decode(generated_ids, skip_special_tokens=True)[0]
        captions.append(caption.strip())
    
    return captions

def summarize_video(captions):
    """캡션들을 하나의 영어 문장으로 요약"""
    if not captions:
        return "No content detected in the video."
    
    # 중복 제거 후 가장 대표적인 캡션 선택
    unique_captions = list(set(captions))
    if len(unique_captions) <= 2:
        return " and ".join(unique_captions)
    
    # 주요 장면들 결합
    main_scenes = unique_captions[:3]
    return f"A video featuring {', '.join(main_scenes[:-1])} and {main_scenes[-1]}."

def main(video_path):
    print(f"Processing video: {video_path}")
    
    # 디바이스 및 모델 설정
    device = setup_device()
    print(f"Using device: {device}")
    
    model, feature_extractor, tokenizer = load_model(device)
    
    # 프레임 추출
    frames = extract_key_frames(video_path, max_frames=8)
    print(f"Extracted {len(frames)} key frames")
    
    # 캡션 생성
    captions = generate_captions(model, feature_extractor, tokenizer, frames, device)
    print("Generated captions:", captions)
    
    # 최종 요약
    summary = summarize_video(captions)
    print("\n" + "="*50)
    print("VIDEO SUMMARY (영어 한 문장):")
    print(summary)
    print("="*50)
    
    return summary

if __name__ == "__main__":
    if len(sys.argv) != 2:
        print("Usage: python video_caption.py <video_path>")
        sys.exit(1)
    
    video_path = sys.argv[1]
    main(video_path)
</pre>

### 실행 결과

<pre class="prettyprint">
$ python video.py example6.mp4

Processing video: example6.mp4
Using device: mps
Extracted 9 key frames
The attention mask is not set and cannot be inferred from input because pad token is same as eos token. As a consequence, you may observe unexpected behavior. Please pass your input's `attention_mask` to obtain reliable results.
We strongly recommend passing in an `attention_mask` since your input_ids may be padded. See https://huggingface.co/docs/transformers/troubleshooting#incorrect-output-when-padding-tokens-arent-masked.
You may ignore this warning if your `pad_token_id` (50256) is identical to the `bos_token_id` (50256), `eos_token_id` (50256), or the `sep_token_id` (None), and your input is not padded.
Generated captions: ['a man riding a wave on top of a surfboard', 'a man riding a wave on top of a surfboard', 'a man riding a wave on top of a surfboard', 'a man riding a wave on top of a surfboard', 'a man riding a wave on top of a surfboard', 'a man riding a wave on top of a surfboard', 'a man riding a wave on top of a surfboard', 'a man riding a wave on top of a surfboard', 'a surfer riding a wave in the ocean']

==================================================
VIDEO SUMMARY (영어 한 문장):
a surfer riding a wave in the ocean and a man riding a wave on top of a surfboard
==================================================
</pre>
