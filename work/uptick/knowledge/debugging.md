To debug local queries, go into `abas/settings/local.py`

And uncomment

```
# Log database calls
#
# LOGGING["handlers"]["console"]["level"] = "DEBUG"
# LOGGING["loggers"]["django.db.backends"] = {
#     "level": "DEBUG",
#     "handlers": ["console"],
# }

```