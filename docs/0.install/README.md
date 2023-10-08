# ubuntu

1. Microsoft Store 에서 ubuntu 설치
2. ubuntu 실행 후 아이디, 패스워드 설정


# java

설치

```
sudo apt-get update
sudo apt install default-jre
sudo apt install default-jdk
```

버전 확인

```
java -version
```

# elasticsearch

설치

```
curl -fsSL https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee -a /etc/apt/sources.list.d/elastic-8.x.list
sudo apt update
sudo apt install elasticsearch
```

# 트러블슈팅

### System has not been booted with systemd as init system (PID 1). Can't operate.

> wsl2 환경이 아니라면 wsl2 환경으로 바꿔준다.

1. 관리자 권한으로 Windows Powershell 실행
2. 아래 명령어 실행

```
dism.exe /online /enable-feature /featurename:Microsoft-Windows-Subsystem-Linux /all /norestart
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

3. 재부팅
4. 버전 업데이트

```
wsl --set-version Ubuntu 2
```

5. 확인

```
wsl --list --verbose
```
