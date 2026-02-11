```python
import time
```

### Timestamp
```python
print(time.time()) # 1770778514.831312
```

### Timestamp conver to Time
```python
import time
from datetime import datetime, timezone

timestamp = time.time()
dt_utc = datetime.fromtimestamp(timestamp, tz=timezone.utc)
print(dt_utc)
```

### Concert to location time
```python
import time
from datetime import datetime, timezone
from zoneinfo import ZoneInfo

timestamp = time.time()
utc_time = datetime.fromtimestamp(timestamp, tz=timezone.utc)
local_time = utc_time.astimezone(ZoneInfo("America/Los_Angeles"))
print(local_time)
```

### Only time part
```python
local_time = utc_time.astimezone(ZoneInfo("America/Los_Angeles"))
time_str = local_time.strftime("%H:%M:%S")
print(time_str)
```