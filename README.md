# Ollama_deepseek_toyproject
ollama + deepseek (7b) 를 맥에서 구동해보고, OpenWebUI까지 띄워서 chatgpt같이 웹ui로 해볼 수 있게 하려는 토이프젝(ai 소스코드나 이런게 아니라 어떤식으로 하니 됐다 메모)

# 1. ollama 설치
```
#brew 사용
brew install ollama

#정상적 설치 확인
ollama --version

# Ollama를 macOS 서비스로 실행
# 만약 다른 운영체제라면 거기 맞게 백그라운드 실행 해야
brew services start ollama

#실행 후 서비스 상태 확인
brew services list

#응답을 주는지 테스트 curl 날려보기
curl http://localhost:11434/api/tags
```

# 2. 딥시크 모델 pull
```
#어떤 이미지 당겨올지는 ollama 공홈 model에서 검색하거나,
#웹서핑하면서 괜찮아 보이는 모델 찾아서.
ollama pull deepseek-r1:7b
```

# 3. 실행
```
#여기까지 되었다면 
#터미널에서 프롬프트를 전달하고 응답받고는 가능
ollama run deepseek-r1:7b
```

# 4. Open WebUI 
```
#위에까지 되었다면 Open WebUI를 통해 웹에서 돌려보고자 함.
#docker가 설치되고 사용 가능한 상태여야 하는데 도커관련 설명은 생략.

# https://github.com/open-webui/open-webui 공식 readme 참고
#If Ollama is on your computer, use this command
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main

#-p 3000:3000 → 웹 브라우저에서 http://localhost:3000으로 접속 가능
#OLLAMA_API_BASE_URL=http://host.docker.internal:11434 → Ollama와 연동
#다른 부분이 있으면 적절히 알아서 수정

#http://localhost:3000 으로 접속 후, Ollama 모델 선택
```

# 5. 자동 실행 스크립트
start_ollama_webui.sh
```
#!/bin/bash

echo "Starting Ollama..."
brew services start ollama

echo "Starting Open WebUI..."
docker run -d -p 3000:8080 --add-host=host.docker.internal:host-gateway -v open-webui:/app/backend/data --name open-webui --restart always ghcr.io/open-webui/open-webui:main

echo "All services started. Access WebUI at http://localhost:3000"
```

```
#실행 예
chmod +x start_ollama_webui.sh
./start_ollama_webui.sh
```

# 6. ollama 캐시해제, 프로세스 종료 원할 시
```
#ollama 가 캐시한 모델 해제
ollama stop

#ollama 강제 종료 후 vram 해제
brew services stop ollama
```