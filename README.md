## Spark UI

### Docker를 사용하여 Spark History Server & Spark UI 확인하기
Loacl을 사용할 경우(EC2 및 Cloud를 사용할 수 없음) Local Docker를 사용하여 Spark History Server & Spark UI를 확인 할 수 있습니다.

**전제 조건**
- Spark History Server 개념
- Spark UI 개념
- Docker 명령어 개념
- Docker 설치

1. Dockerfile과 pom.xml를 다운로드합니다.
2. 도커 빌드
    ```bash
    docker build -t glue/sparkui:latest .
    ```
3. 상황에 맞는 방법을 선택합니다.
   1. AWS profile 사용
    - 환경 변수
        ```bash
        LOG_DIR="s3a://spark-ui-log/"
        PROFILE_NAME="profile_name"
        ```
      - s3a://spark-ui-log를 S3 디렉터리로 바꿔서 설정합니다.
      - AWS profile를 설정합니다.
    - Docker Run
        ```bash
        docker run -itd -v ~/.aws:/root/.aws -e AWS_PROFILE=$PROFILE_NAME -e SPARK_HISTORY_OPTS="$SPARK_HISTORY_OPTS -Dspark.history.fs.logDirectory=$LOG_DIR  -Dspark.hadoop.fs.s3a.aws.credentials.provider=com.amazonaws.auth.DefaultAWSCredentialsProviderChain" -p 18080:18080 glue/sparkui:latest "/opt/spark/bin/spark-class org.apache.spark.deploy.history.HistoryServer"
        ```

    2. AWS AWS_ACCESS_KEY_ID, AWS_SECRET_ACCESS_KEY 사용
    - 환경 변수
        ```bash
        LOG_DIR="s3a://spark-ui-log/"
        AWS_ACCESS_KEY_ID="AKIAxxxxxxxxxxxx"
        AWS_SECRET_ACCESS_KEY="yyyyyyyyyyyyyyy"
        ```
      - s3a://spark-ui-log를 S3 디렉터리로 바꿔서 설정합니다.
      - AWS_ACCESS_KEY_ID를 설정합니다.
      - AWS_SECRET_ACCESS_KEY를 설정합니다.
    - Docker Run
        ```bash
        docker run -itd -e SPARK_HISTORY_OPTS="$SPARK_HISTORY_OPTS -Dspark.history.fs.logDirectory=$LOG_DIR -Dspark.hadoop.fs.s3a.access.key=$AWS_ACCESS_KEY_ID -Dspark.hadoop.fs.s3a.secret.key=$AWS_SECRET_ACCESS_KEY" -p 18080:18080 glue/sparkui:latest "/opt/spark/bin/spark-class org.apache.spark.deploy.history.HistoryServer"
        ```

    3. AWS Temporary Credentials 사용
    - 환경 변수
        ```bash
        LOG_DIR="s3a://spark-ui-log/"
        AWS_ACCESS_KEY_ID="AKIAxxxxxxxxxxxx"
        AWS_SECRET_ACCESS_KEY="yyyyyyyyyyyyyyy"
        AWS_SESSION_TOKEN="zzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzzz"
        ```
      - s3a://spark-ui-log를 S3 디렉터리로 바꿔서 설정합니다.
      - AWS_ACCESS_KEY_ID를 설정합니다.
      - AWS_SECRET_ACCESS_KEY를 설정합니다.
      - AWS_SESSION_TOKEN를 설정합니다.
    - Docker Run
        ```bash
        docker run -itd -e SPARK_HISTORY_OPTS="$SPARK_HISTORY_OPTS -Dspark.history.fs.logDirectory=$LOG_DIR -Dspark.hadoop.fs.s3a.access.key=$AWS_ACCESS_KEY_ID -Dspark.hadoop.fs.s3a.secret.key=$AWS_SECRET_ACCESS_KEY -Dspark.hadoop.fs.s3a.session.token=$AWS_SESSION_TOKEN -Dspark.hadoop.fs.s3a.aws.credentials.provider=org.apache.hadoop.fs.s3a.TemporaryAWSCredentialsProvider" -p 18080:18080 glue/sparkui:latest "/opt/spark/bin/spark-class org.apache.spark.deploy.history.HistoryServer"
        ```

4. 브라우저에서 http://localhost:18080을 엽니다.