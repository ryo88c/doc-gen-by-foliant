FROM foliant/foliant:full

COPY docker/requirements.txt .

RUN git config --global --add safe.directory /usr/src/app \
    && pip3 install -r requirements.txt
