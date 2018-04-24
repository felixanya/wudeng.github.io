# Token Bucket Rate Limiter

* key: a unique byte string that identifies the bucket
* maxAmount: the maximum number of tokens the bucket can hold
* refillTime: the amount of time between refills
* refillAmount: the number of tokens that are added to the bucket during a refill
* value: the current number of tokens in the bucket
* lastUpdate: the last time the bucket was updated



```
// create:
bucket.value = bucket.maxAmount
bucket.lastUpdate = now()

refillCount = floor((now() - bucket.lastUpdate) / bucket.refillTime)
bucket.value = min(
    bucket.maxAmount,
    bucket.value + refillCount * bucket.refillAmount
)
bucket.lastUpdate = min(
    now(),
    bucket.lastUpdate + refillCount * bucket.refillTime
)
```


* https://gist.github.com/petehunt/08c9fef5703e79ca26ceed845163dcdc
