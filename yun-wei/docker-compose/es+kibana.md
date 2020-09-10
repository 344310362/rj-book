
version: '3'
services:
  redis:
    image: redis
    ports:
      - "6380:6379"
    networks: 
      - webservice_web-service
    cap_add: 
    - NET_ADMIN
  redis-stat:
    image: richardhull/redis-stat
    ports: 
      - "63790:63790"
    command: ["--server" ,"redis:6379"]
    networks: 
      - webservice_web-service
  elasticsearch:
    image: docker.elastic.co/elasticsearch/elasticsearch:6.3.2
    environment:
      - cluster.name=docker-cluster
      - bootstrap.memory_lock=true
      - "ES_JAVA_OPTS=-Xms512m -Xmx512m"
    ulimits:
      memlock:
        soft: -1
        hard: -1
    ports:
      - "9200:9200"
    networks: 
      - webservice_web-service
  kibana:
    image: docker.elastic.co/kibana/kibana:6.3.2
    ports:
      - "5601:5601"
    networks: 
      - webservice_web-service
  mongo:
    image: mongo
    # restart: always
    command: ["--replSet","my-mongo-set"]
    environment:
      MONGO_INITDB_ROOT_USERNAME: root
      MONGO_INITDB_ROOT_PASSWORD: asdqwe123
    ports: 
      - "27017:27017"
    networks: 
      - webservice_web-service
  mongo-connector:
    image: mgebundy/docker-mongo-connector
    restart: always
    environment: 
      MONGO: mongo
      ELASTICSEARCH: elasticsearch
    networks: 
      - webservice_web-service
networks: 
  webservice_web-service:
    external: true