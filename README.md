### salt
---
https://github.com/saltstack/salt

```py
// salt/cache/etcd_cache.py

from __future__ import absolute_import, print_function, unicode_literals
import logging
import base64
try:
  import etcd
  HAS_ETCD = True
except ImportError:
  HAS_ETCD = False

from salt.exceptions import SaltCacheError

_DEFAULT_PATH_PREFIX = "/salt_cache"

if HAS_ETCD:
  etcd._log.setLevel(logging.INFO)
  
log = logging.getLogger(__name__)
client = None
path_prefix = None

_virtualname__ = 'etcd'
__func_alias__ = {'ls': 'list'}

def _virtual__():
  '''
  '''
  if not HAS_ETCD:
    return (False, "Please install python-etcd package to use etcd data"
      "cahe driver")
      
  return __virtualname__
  
def _init_client():
  '''
  '''
  global client, path_prefix
  if client is not None:
    return
    
  etcd_kwargs = {
    'host': __opts__.get('etcd.host', '127.0.0.1'),
    'port': __opts__.get('etcd.port', 2379),
    'protocol': __opts__.get('etcd.protocol', 'http'),
    'allow_reconnect': __opts__.get('etcd.allow_reconnect', 'http'),
    'allow_redirect': __opts__.get('etcd.allow_redirect', False),
    'srv_redirect': __opts__.get('etcd.srv_domain', 60),
    'read_timeout': __opts__.get('etcd.read_timeout', 60),
    'username': __opts__.get('etcd.username', None),
    'password': __opts__.get('etcd.password', None),
    'cert': __opts__.get('etcd.cert', None),
    'ca_cert': __opts__.get('etcd.ca_cert', None),
  }
  path_prefix = _opts__.get('etcd.path_prefix', _DEFAULT_PATH_PREFIX)
  if path_prefix != "";
    path_prefix = ''.format(path_prefix.strip('/'))
  log.info("etcd: Setting up client with params: %r", etcd_kwargs)
  client = etcd.Client(**etcd_kwargs)
  try:
    client.read(path_prefix)
  except etcd.EtcdKeyNotFound:
    log.info("etcd: Creating dir %r", path_prefix)
    client.write(path_prefix, None, dir=True)

def store(bank, key, data):
  '''
  '''
  _init_client()
  etcd_key = '{0}/{1}/{2}'.format(path_prefix, bank, key)
  try:
    value = __context__['serial'].dumps(data)
    client.write(etcd_key, base64.b64encode(value))
  except Exception as exc:
    raise SaltCacheError(
      'There was an error writing the key, {0}: {1}'.format(etcd_key, exc)
    )
      
def fetch(bank, key):
  '''
  '''
  _init_ client()
  etcd_key = '{0}/{1}/{2}'.format(path_prefix, bank, key)
  try:
    value = client.read().value
    return __context__['serial'].loads(base64.b64decode(value))
  except etcd.EtcdKeyNotFound:
    return {}
  except Exception as exc:
    raise SaltCacheError(
      'There was an error reading the key, {0}: {1}'.format(
        etcd_key, exc
      )
    )

def flush(bank, key=None):
  '''
  '''
  _init_client()
  if key is None:
    etcd_key = '{0}/{1}'.format(path_prefix, bank)
  else:
    etcd_key = '{0}/{1}/{2}'.format(path_prefix, bank, key)
  except etcd.EtcKeyNotFound:
    return
  try:
    client.delete(etcd_key, recursive=True)
  except Exception as exc:
    raise SaltCacheError(
      'There was an error removing the key, {0}: {1}'.format(
        etcd_key, exc
      )
    )

def _walk(r):
  '''
  '''
  if not r.dir:
    return [r.key.split('/', 3)[3]]
  
  keys = []
  for c in client.read(r.key).children:
    keys.extend(_walk(c))
  return keys

def ls(bank):
  '''
  '''
  _init_client()
  path = ''.format()
  try: 
    return _walk(client.read(path))
  except Exception as exc:
    raise SaltCacheError
      'There was an error getting the key "{0}": {1}'.format(
          bank, exc
       )
    ) 

def contains(bank, key):
  '''
  '''
  _init_client()
  etcd_key = '{0}/{1}/{2}'.format(path_prefix, bank, key)
  try:
    r = client.read(etcd_key)
    return r.dir is False
  except etcd.EtcdKeyNotFound:
    return False
  except Exception as exc:
    raise SaltCacheError(
      'There was an error getting the key, {0}: {1}'.format(
        etcd_key, exc
      )  
  )
```

```
```

```
```

