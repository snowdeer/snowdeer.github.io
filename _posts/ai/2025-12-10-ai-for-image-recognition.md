---
layout: post
title: 이미지 인식 AI 모델(ViT) 테스트
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
pip install torch torchvision torchaudio --index-url https://download.pytorch.org/whl/cpu
pip install transformers pillow
</pre>

### 사용 가능 디바이스 확인

Apple Silicon 환경에서는 `mps` 디바이스 사용 가능

<pre class="prettyprint">
import torch

if torch.backends.mps.is_available():
    device = torch.device("mps")   # Apple GPU
elif torch.cuda.is_available():
    device = torch.device("cuda")
else:
    device = torch.device("cpu")

print("Using device:", device)
</pre>

### ViT 기반 모델

Hugging Face의 `google/vit-base-patch16-224`는 ImageNet으로 사전학습된 표준 ViT로,
맥북에서도 작은 배치로 실행 가능한 모델임.
만약 더 가벼운 모델이 필요할 경우에는 `google/vit-small-patch16-224`나 `Distilled ViT` 계열도 선택 가능함

### main.py

아래는 `input.jpg` 파일을 읽어서 어떤 그림인지 키워드를 리턴해주는 코드

<pre class="prettyprint">
import torch
from PIL import Image
from transformers import AutoImageProcessor, ViTForImageClassification

# 1) 디바이스 선택 (위에서 설명한 로직 재사용 가능)
if torch.backends.mps.is_available():
    device = torch.device("mps")
elif torch.cuda.is_available():
    device = torch.device("cuda")
else:
    device = torch.device("cpu")
print("Using device:", device)

# 2) 모델 이름 선택 (맥북에서도 무난한 베이스 모델)
model_name = "google/vit-base-patch16-224"

# 3) 이미지 전처리기와 모델 로드
image_processor = AutoImageProcessor.from_pretrained(model_name)
model = ViTForImageClassification.from_pretrained(model_name).to(device)
model.eval()

# 4) 이미지 로드 (input.jpg 경로에 이미지 준비)
image_path = "input.jpg"
image = Image.open(image_path).convert("RGB")

# 5) 전처리 및 텐서 변환
inputs = image_processor(image, return_tensors="pt")
inputs = {k: v.to(device) for k, v in inputs.items()}

# 6) 추론
with torch.no_grad():
    outputs = model(**inputs)
    logits = outputs.logits
    probs = logits.softmax(dim=-1)
    top_prob, top_idx = probs.max(dim=-1)

# 7) 결과 해석
predicted_label_idx = top_idx.item()
predicted_prob = top_prob.item()
predicted_label_name = model.config.id2label[predicted_label_idx]

print(f"Predicted class: {predicted_label_name} (p={predicted_prob:.4f})")
</pre>

### 실행 결과

<pre class="prettyprint">
Using device: mps
Fast image processor class &lt;class 'transformers.models.vit.image_processing_vit_fast.ViTImageProcessorFast'&gt; is available for this model. Using slow image processor class. To use the fast image processor class set `use_fast=True`.
Predicted class: banana (p=0.9748)
</pre>
