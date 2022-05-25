# 테스트서버 작성

> 파이썬 기반 Flask 서버로 테스트서버를 구축

1. GitHub에 원하는 프로젝트명으로 레포지토리를 생성
2. Visual Studio Code에서 crtl + shift + p를 누른 후 clone 입력
3. 1번에서 만들었던 레포지토리 주소를 입력
4. app.py라는 파일 하나를 작성
    ```
    # app.py

    from flask import Flask

    app = Flask(__name__)


    @app.route('/')
    def main():
        return 'Hello From Flask'


    if __name__ == '__main__':
        app.run(host='0.0.0.0', port=8080)
    ```
    > 인증서는 없으니 주소가 ``` http:// ```로 시작하는지 확인할 것\
    > 파이썬 문법에 대해서는 인터넷 검색

5. 로컬에서 작동하는 것을 확인하고 requirements.txt파일을 생성
    ```
    pip freeze > requirements.txt
    ```

6. Dockerfile을 생성해서 아래 내용을 입력
    ```
    # python:3.8의 이미지로 부터
    FROM python:3.8
    # 제작자 및 author 기입
    LABEL maintainer="auma-@naver.com"

    # 해당 디렉토리에 있는 모든 하위항목들을 '/app/server`로 복사한다
    COPY . /app/server

    # image의 directory로 이동하고
    WORKDIR /app/server

    # 필요한 의존성 file들 설치
    RUN pip3 install -r requirements.txt

    # 환경 설정 세팅
    RUN python setup.py install

    # container가 구동되면 실행
    ENTRYPOINT ["python", "app.py"]
    ```
    > [참고한 티스토리](https://huisam.tistory.com/entry/Dockerfile)

7. buildspec.yaml을 생성해서 아래 내용을 입력
    ```
    version: 0.2
    phases:
    pre_build:
        commands:
        - echo Logging in to Amazon ECR..
        - aws ecr get-login-password --region <ecr 리전> | docker login --username AWS --password-stdin "$(aws sts get-caller-identity --query Account --output text).dkr.ecr.<ecr 리전>.amazonaws.com"
        - DOCKER_IMAGE=<AWS-account>.dkr.ecr.<ecr 리전>.amazonaws.com/test
        - echo -----------------------------
        - IMAGE_TAG=$(TZ='Asia/Seoul' date +"%y%m%d-%H%M")
    build:
        commands:
        - echo Build started on `date`
        - echo Building the Docker image...
        - echo Image_tag $IMAGE_TAG
        - docker build -t $DOCKER_IMAGE:latest .

        - docker tag $DOCKER_IMAGE:latest $DOCKER_IMAGE:$IMAGE_TAG

    post_build:
        commands:
        - echo Build completed on `date`
        - echo Pushing the Docker images...
        - docker push $DOCKER_IMAGE:$IMAGE_TAG
    ```
    > ecr을 생성한 리전과 aws account를 꼭 변경하세요