### Args and Kwargs
It can be seen that args and kwargs are set on the message of the BGTask. This is set through `delay` in `send_with_options`

```python
def delay(self, *args: P.args, **kwargs: P.kwargs) -> MessageProxy:
        from django.conf import settings
        delay = kwargs.pop("delay", None)
        if settings.IN_TESTS:
            print(f"  WARNING: Unmocked BGTask encountered: {self.actor_name}")  
        return MessageProxy(
            self.send_with_options(args=args, delay=delay, kwargs=kwargs)  # 
	)
```