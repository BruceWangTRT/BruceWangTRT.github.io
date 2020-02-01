---
categories: [python]
tags: [python, logging]
---

# Python Logging 02 Flow

Still remember this flow chart?
![](/images/python_stdlib_logging.png "logging flow chart")

Now that we know those concepts, we can dig into the details and
start to understand the dynamics.

# logger flow
{: .no_toc}

Everything starts from logger methods like `log()`, `info()`, `warning()`, etc.

```python
import logging
logger = logging.getLogger("some_name")
logger.warning("some message")
```

Few things will happen in this order:
1. TOC
{:toc}

## check if requested severity level is enabled

This is done by checking first the module-level level set by `logging.disable(level)` and then the logger’s effective level.

The logger hierarchy is traversed towards the root logger until a value other than `NOTSET` is found or reached the root logger whose effective level is always set to `WARNING`.

In case of no disable settings, if the requested severity level is greater than or equal to the logger's effective level, the request will proceed. Nothng happens otherwise.

## create an instance of LogRecord if enabled

An instance of `LogRecord`(the record) is created at this stage with all the information it gathers at the point of creation, including severity level, logging message string, name of the logger, the full pathname of the source file where the logging call was issued (if available), etc. The record is all the rest of the flow can see. In other words, it is the sole source of information for filters, handlers and formatters.

## check the LogRecord instance against filters

Apply this logger’s filters to the record and return `True` if the record is to be processed. The filters are consulted in turn, until one of them returns a false value. If none of them return a false value, the record will be processed (passed to handlers). If one returns a false value, no further processing of the record occurs.

## invoke handlers of logger instance

### check severity level again

Why is there another level check? The level set in the logger determines which severity of messages it will pass to its handlers. The level set in each handler determines which messages that handler will send on.

### filter again

Similar to serverity level check, filters added to each handler determines which message that handler will send on

### format 

Formatters can always be configured beforehand. But when to do the formatting is up to the implementation of handlers.

### emit

This is the step to do whatever it takes to actually log the specified logging record. The log could be written to a file as a string, or sent over a socket as a Python pickle. The standard library includes quite a few handler types (see [Useful Handlers](https://docs.python.org/3/howto/logging.html#useful-handlers))


## propagate if configured

Each logger has an attribute named propagate defaulting to true. If this attribute evaluates to true, events logged to this logger will be passed to the handlers of higher level (ancestor) loggers, in addition to any handlers attached to this logger. Messages are passed directly to the ancestor loggers’ handlers - **neither the level nor filters of the ancestor loggers in question are considered**.

If this evaluates to false, logging messages are not passed to the handlers of ancestor loggers.

If you attach a handler to a logger and one or more of its ancestors, it may emit the same record multiple times. In general, you should not need to attach a handler to more than one logger - if you just attach it to the appropriate logger which is highest in the logger hierarchy, then it will see all events logged by all descendant loggers, provided that their propagate setting is left set to True. A common scenario is to attach handlers only to the root logger, and to let propagation take care of the rest.
