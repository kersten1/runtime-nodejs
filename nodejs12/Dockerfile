FROM openwhisk/action-nodejs-v12:7aca443

COPY ./package.json /

RUN apt-get update \
    # Update some installed packages to get security fixes.
    && apt-get install -y --no-install-recommends e2fsprogs file git openssl subversion \
    # Cleanup apt data, we do not need them later on.
    && rm -rf /var/lib/apt/lists/* \
    # We do not have mysql-server installed but mysql-common contains config files (/etc/mysql/my.cnf) for it.
    # We need to add some dummy entries to /etc/mysql/my.cnf to sattisfy vulnerability checking of it.
    && echo "\n[mysqld]\nssl-ca=/tmp/ca.pem\nssl-cert=/tmp/server-cert.pem\nssl-key=/tmp/server-key.pem\n" >> /etc/mysql/my.cnf \
    # Start adding/updating npm packages.
    && cd / \
    && npm install --production \
    # Do some cleanup of not used npm install artifacts.
    && npm cache clean --force \
    && rm -rf /root/.cache/node-gyp \
    # Replace default openwhisk main with an iam enabled version.
    && sed -i.bak 's/lib\/main.js/lib\/iam-openwhisk-main.js/' /node_modules/openwhisk/package.json

COPY iam-client/iam-openwhisk-main.js /node_modules/openwhisk/lib/
