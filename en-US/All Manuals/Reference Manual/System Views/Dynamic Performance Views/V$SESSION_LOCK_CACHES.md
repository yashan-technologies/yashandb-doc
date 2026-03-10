This view shows the information of all current SESSION LOCK CACHES.

|Field |Type |Description |
| --- | --- | --- |
| SID | INTEGER | Session ID |
| CACHE_TYPE | VARCHAR(16) | LOCK CACHE type: TABLE CACHE, BLOCK CACHE |
| TOTAL | BIGINT | Total number of LOCK CACHES generated (including the current number and the number that has been reused) |
| HITS | BIGINT | Number of LOCK CACHE hits |
| INVALIDS | BIGINT | Number of LOCK CACHE invalidations |
| MISS | BIGINT | Number of LOCK CACHE misses |
| COUNT | BIGINT | Total number of current LOCK CACHES |