# Avanti DEV environment
Install DEV environment
``` bash
git clone git@github.com:IONlu/avanti-dev.git --recursive && \
cd avanti-dev && \
cd avanti-core && \
git checkout develop && \
cd ../avanti-cli && \
git checkout develop && \
cd ../avanti-api && \
git checkout develop && \
cd .. && \
vagrant up
```
