Example of helm values.yaml file for deployement of TreC microservices in Kubernetes
```
global:
 mariadb:
     # Use external MariaDB in Azure
    host: <db host address>
    rootUsername: <root username>
    dbPrefix: <db prefix>
 mongodb:
    atlasEnabled: true
 ingress:
    hosts:
    - name: <host address
      tlsSecretName: <tls secret name>

audit-logger:
 env:
   # Connection to the MariaDB database
   - name: DB_URL
     value: mariadb:<microservice db url>
   - name: DB_USERNAME
     value: <db username> 	# For Azure we need to append the `@servername` part
   - name: DB_PASSWORD 	# Read from the Kubernetes secret created by init script
     valueFrom:
       secretKeyRef:
         name: audit-logger-db-credentials
         key: password

blob-storage:
 env:
  # Connection to database
  - name: DB_URL
    value: mariadb:< microservice db url>
  - name: DB_USERNAME
    value: <db username> 	# For Azure we need to append the `@servername` part
  - name: DB_PASSWORD 		# Read from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: blob-storage-db-credentials
        key: password
  # Activate Spring profiles
  - name: SPRING_PROFILES_ACTIVE
    value: "jsonlog,production"
  # Connection to Minio inside Kubernetes
  - name: TREC_BLOB_STORAGE_MINIO_ENDPOINT
    value: http://minio:9000
  - name: TREC_BLOB_STORAGE_MINIO_ACCESS_KEY
    value: <minio access key>
  - name: TREC_BLOB_STORAGE_MINIO_SECRET_KEY
    value: <minio secret key>

chat:
 env:
  # Connection to the MariaDB database
  - name: DB_URL
    value: mariadb:<microservice db url>
  - name: DB_USERNAME
    value: <db username > 	# For Azure we need to append the `@servername` part
  - name: DB_PASSWORD 		# Read from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: chat-db-credentials
        key: password
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbit virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: chat-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: chat-rabbitmq-credentials
        key: password

clinical-data:
 env:
    # Connection string to MongoDB Atlas
  - name: MONGO_CONNECTION_STRING
    value: <mongo db connection string>
    # The user is managed inside MongoDB Atlas
  - name: MONGO_USERNAME
    value: <db username>
  - name: MONGO_DB_NAME
    value: <microservice db name>
  - name: MONGO_PASSWORD 	# Read username from the Kubernetes secret created by mongodb-init subchart
    valueFrom:
      secretKeyRef:
        name: clinical-data-mongodb-credentials
        key: password
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbit virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: clinical-data-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: clinical-data-rabbitmq-credentials
        key: password
 mongodb-init:
    password: <password> 	# Password for the database user
    atlasEnabled: true

cloud-data-collector:
 env:
  # Connection to the MariaDB database
  - name: DB_URL
    value: mariadb:<microservice db url>
  - name: DB_USERNAME
    value: <db username> 	# For Azure we need to append the `@servername` part
  - name: DB_PASSWORD 	# Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: cloud-data-collector-db-credentials
        key: password
  - name: SPRING_PROFILES_ACTIVE
    value: jsonlog

dictionary:
 env:
  # Enable Java remote debug port
  - name: JAVA_OPTIONS
    value: -agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005,quiet=y
  # Connection to the MariaDB database
  - name: DB_URL
    value: mariadb:<microservice db url>
  - name: DB_USERNAME
    value: <db username> 	# For Azure we need to append the `@servername` part
  - name: DB_PASSWORD 	# Read password from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: dictionary-db-credentials
        key: password

drug-store:
 env:
  # Connection to the MariaDB database
  - name: DB_URL
    value: mariadb:<microservice db url>
  - name: DB_USERNAME
    value: <db username> 	# For Azure we need to append the `@servername` part
  - name: DB_PASSWORD 		# Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: drug-store-db-credentials
        key: password
  # Enable Java remote debug port
  - name: JAVA_OPTIONS
    value: "-agentlib:jdwp=transport=dt_socket,server=y,suspend=n,address=5005,quiet=y"

email:
 env:
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbitmq virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: email-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: email-rabbitmq-credentials
        key: password
  # SMTP server configuration
  - name: MAIL_SERVER_URL
    value: <server url>
  - name: MAIL_SERVER_PORT
    value: <server port>
  - name: SSL_ENABLED
    value: <true>|<false>

password-authenticator:
 env:
  # Connection to database
  - name: DB_URL
    value: mariadb:<microservice db url>
  - name: DB_USERNAME
    value: <db username> 	# For Azure we need to append the `@servername` part
  - name: DB_PASSWORD
    valueFrom:
      secretKeyRef:
        name: password-authenticator-db-credentials
        key: password
  # Connection to Redis
  - name: SPRING_REDIS_HOST
    value: redis
  - name: SPRING_REDIS_PORT
    value: <redis port>
  # Spring mail configuration
  - name: SPRING_MAIL_HOST
    value: <spring mail host>
  - name: SPRING_MAIL_PORT
    value: <spring mail port>
  - name: SPRING_MAIL_PROPERTIES_MAIL_SMTP_SSL_ENABLE
    value: <true>|<false>
  # App properties
  - name: TREC_PASSWORD_AUTHENTICATOR_BASE_URL
    value:<password authenticator url>
  - name: TREC_PASSWORD_AUTHENTICATOR_EMAIL_SENDER
    value:<password authenticator email sender >
  - name: TREC_PASSWORD_AUTHENTICATOR_EMAIL_DOMAIN
    value:<password authenticator email domain>
  - name: TREC_PASSWORD_AUTHENTICATOR_ADMIN_USERNAME
    value:<password authenticator admin username>
  - name: TREC_PASSWORD_AUTHENTICATOR_ADMIN_EMAIL
    value:<password authenticator admin email>
  - name: TREC_PASSWORD_AUTHENTICATOR_TWO_FA_ENFORCE2FA
    value:<true>|<false>

perseo-messaging:
 env:
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbit virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: perseo-messaging-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: perseo-messaging-rabbitmq-credentials
        key: password

perseo-proempower:
 env:
    # Connection string to MongoDB Atlas cluster
  - name: MONGO_CONNECTION_STRING
    value: <connection string>
    # The user is managed inside MongoDB Atlas
  - name: MONGO_USERNAME
    value: <db username>
  - name: MONGO_DB_NAME
    value: <microservice db name>
  - name: MONGO_PASSWORD # Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: perseo-proempower-mongodb-credentials
        key: password
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbit virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: perseo-proempower-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: perseo-proempower-rabbitmq-credentials
        key: password
    # mail from perseo configuration
  - name: FROM_ADDRESS_NAME
    value: <sender address name>
  - name: FROM_ADDRESS
    value: <sender address>
  - name: TO_ADDRESS_NAME
    value: <recipient address name>
  - name: TO_ADDRESS
    value: <recipient address(es)>
  - name: BASE_URL
    value: <sender base url>
 mongodb-init:
    password: <db user password> 	# Password for the database user
    atlasEnabled: true

personal-data:
 env:
    # Connection string to MongoDB
  - name: MONGO_CONNECTION_STRING
    value: <connection string>
  - name: MONGO_DB_NAME
    value: <microservice db name>
  - name: MONGO_USERNAME
    value: <db username>
  - name: MONGO_PASSWORD	# Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: personal-data-mongodb-credentials
        key: password
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbit virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: personal-data-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: personal-data-rabbitmq-credentials
        key: password
 mongodb-init:
    password: <user db password> 	# Password for the database user
    atlasEnabled: true

phonecode-authenticator:
 env:
  # Connection to database
  - name: INTERNAL_KEY_PATH
    value: <internal key file>
  - name: DB_URL
    value: <microservice db url>
  - name: DB_USERNAME
    value: <db username>
  - name: DB_PASSWORD 	# Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: phonecode-authenticator-db-credentials
        key: password
  # Activate Spring profiles
  - name: SPRING_PROFILES_ACTIVE
    value: "jsonlog,production" 
  - name: TREC_SUPPORTED_APPS
    value: <id app 1>,…,<id app n>

push:
 env:
    # Connection string to MongoDB
  - name: MONGO_CONNECTION_STRING
    value: <connection string>
  - name: MONGO_USERNAME
    value: <db username
  - name: MONGO_DB_NAME
    value: <microservice db name>
  - name: MONGO_PASSWOR 	# Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: push-mongodb-credentials
        key: password
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbit virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: push-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: push-rabbitmq-credentials
        key: password
  # Firebase key
  - name: FIREBASE_KEY
    value: <key value>
 mongodb-init:
    password: <db user password>      # Password for the database user
    atlasEnabled: true

reasoner:
 env:
    # Connection string to MongoDB
  - name: MONGO_CONNECTION_STRING
    <connection string>
  - name: MONGO_DB_NAME
    value: <microservice db name>
    # The user is managed inside MongoDB Atlas
  - name: MONGO_USERNAME
    value: <db username>
  - name: MONGO_PASSWORD	# Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: reasoner-mongodb-credentials
        key: password
  # RabbitMQ connection
  - name: RABBITMQ_HOST
    value: rabbitmq
  - name: RABBITMQ_VIRTUALHOST
    value: <rabbit virtual host>
  - name: RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: reasoner-rabbitmq-credentials
        key: username
  - name: RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: reasoner-rabbitmq-credentials
        key: password
 mongodb-init:
    password: <db user password> 	# Password for the database user
    atlasEnabled: true
    
registry:
 env:
    # Connection string to MongoDB Atlas cluster
  - name: MONGO_CONNECTION_STRING
    value: <connection string>

    # The user is managed inside MongoDB Atlas
  - name: MONGO_USERNAME
    value: <db username>
  - name: MONGO_DB_NAME
    value: <microservice db name>
  - name: MONGO_PASSWORD 	# Read username from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: registry-mongodb-credentials
        key: password
 mongodb-init:
    password: <db. User password> 	# Password for the database user
    atlasEnabled: true

session-manager:
 env:
  # Private and public keys for signing TreC tokens
  - name: TOKEN_TREC_PRIVATE_KEY_PATH
    value: <private key path>
  - name: TOKEN_TREC_PUBLIC_KEY_PATH
    value: <public key path>
  # Connection to the MariaDB database
  - name: DB_URL
    value: mariadb:<microservice db url>
  - name: DB_USERNAME
    value: <db username> 	# For Azure we need to append the `@servername` part
  - name: DB_PASSWORD	# Read password from the Kubernetes secret created by init script
    valueFrom:
      secretKeyRef:
        name: session-manager-db-credentials
        key: password

video-chat:
 env:
  # Connection to database
  - name: SPRING_DATA_MONGODB_URI
    # Mongo (Atlas?) requires username and password encoded in the uri string
    value: <uri string>
  # RabbitMQ connection
  - name: SPRING_RABBITMQ_HOST
    value: rabbitmq
  - name: SPRING_RABBITMQ_VIRTUAL_HOST
    value: <rabbit virtual host>
  - name: SPRING_RABBITMQ_USERNAME
    valueFrom:
      secretKeyRef:
        name: video-chat-rabbitmq-credentials
        key: username
  - name: SPRING_RABBITMQ_PASSWORD
    valueFrom:
      secretKeyRef:
        name: video-chat-rabbitmq-credentials
        key: password
  # Jitsi config
  - name: TREC_VIDEO_CHAT_JITSI_SERVER_URL
    value: <jitsi server url>
  - name: TREC_VIDEO_CHAT_JITSI_SECRET_KEY
    value: <jitsi key>
  - name: TREC_VIDEO_CHAT_JITSI_APP_ID
    value: <jitsi app id>
  - name: TREC_VIDEO_CHAT_JITSI_ISSUER_ID
    value: <jitsi issuer id>
  # App config
  - name: TREC_VIDEO_CHAT_APPS_TO_NOTIFY
    value: <id app 1>,…,<id app n>
  # Activate Spring profiles
  - name: SPRING_PROFILES_ACTIVE
    value: "jsonlog"
  # App properties
  - name: TREC_VIDEO_CHAT_ALLOW_JOIN_MEETING_BEFORE_MIN
    value: <n minutes> 	# Allow to join meetings n minutes before the startTime
 mongodb-init:
    password: <db user password>      # Password for the database user
    atlasEnabled: true
```
