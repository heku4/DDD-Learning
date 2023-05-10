# DDD-Learning
Education


## Throttle vs Debounce

They do the exact same thing (rate limiting) but while throttle is being called it fires your function periodically, while debounce just fires once at the end.
Throttle fires throughout, debounce only fires at the end.


Example: If you're scrolling, throttle will slowly call your function while you scroll (every X milliseconds). Debounce will wait until after you're done scrolling to call your function.