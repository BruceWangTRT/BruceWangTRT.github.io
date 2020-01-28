# Python Logging 01 Concepts

There is a logging module in Python standard library.
And the Python org has great documemtations about it [here](https://docs.python.org/3/library/logging.html).
The following contents are mostly my digest.
Let's start with basic concepts.
There are few major concepts in logging standard library:
**severity level**, **logger**, **handler**, **filter**, **formatter**, **LogRecord**.

Table of contents:

1. TOC
{:toc}

# severity level

The standard severity levels and their applicability are described below (in increasing order of severity):

| **Level** | **Numeric value** | **When it’s used** |
| NOTSET | 0 | N/A |
| DEBUG | 10 | Detailed information, typically of interest only when diagnosing problems. |
| INFO | 20 | Confirmation that things are working as expected. |
| WARNING | 30 | An indication that something unexpected happened, or indicative of some problem in the near future (e.g. ‘disk space low’). The software is still working as expected. |
| ERROR | 40 | Due to a more serious problem, the software has not been able to perform some function. |
| CRITICAL | 50 | A serious error, indicating that the program itself may be unable to continue running. |

{% include info.html text="If you define a level with the same numeric value, it overwrites the predefined value; the predefined name is lost." %}

# logger

Logging is performed by calling methods on instances of the Logger class (hereafter called loggers). Each logger instance has a name, and they are referred by it.

```python
>>>import logging
>>>logger = logging.getLogger("some_name")
```

Loggers names are period-separated hierarchical structures.
Loggers that are further down in the hierarchical list are children of loggers higher up in the list. 
For example, given a logger with a name of `parent`, loggers with names of `parent.child` and `parent.child.pet` are all descendants of the logger with the name of`parent`.
The `root` logger is the parent of all loggers. And if you did not pass a name when calling `getLogger()`, a reference to the `root` logger will be returned.

```python
>>>import logging
>>>logging.getLogger()
<RootLogger root (WARNING)>
>>>logger_parent = logging.getLogger("parent")
>>>logger_parent.parent
<RootLogger root (WARNING)>
>>>logger_child = logging.getLogger("parent.child")
>>>logger_child.parent
<Logger parent (WARNING)>
```

A good convention to use when naming loggers is to use a module-level logger, in each module which uses logging, named as follows:

```python
logger = logging.getLogger(__name__)
```

Multiple calls to `getLogger()` with the same name will return a reference to the same logger object.

```python
>>>import logging
>>>logger = logging.getLogger("some_name")
>>>logger_two = logging.getLogger("some_name")
>>>logger == logger_two
True
```

Loggers have a concept of effective level. If a level is not explicitly set on a logger, the level of its parent is used instead as its effective level. This happens recusively until it reaches the root logger, which always has an explicit level set (`WARNING` by default).

# LogRecord

Logging messages are encoded as instances of the LogRecord class. When a logger decides to actually log an event, a LogRecord instance is created from the logging message. Log event information is passed between loggers, handlers, filters and formatters in LogRecord instances. 

# handler

Handler objects are responsible for dispatching the appropriate log messages to the handler’s specified destination. Logger objects can add zero or more handler objects to themselves with an `addHandler()` method.

```python
import logging

# create logger
logger = logging.getLogger('simple_example')

# create console handler
ch = logging.StreamHandler()

# add ch to logger
logger.addHandler(ch)

# 'application' code
logger.error('error message')
```

The standard library includes quite a few handler types (see [Useful Handlers](https://docs.python.org/3/howto/logging.html#useful-handlers))

# filter

Filters can be used by handlers and loggers for more sophisticated filtering than is provided by levels. See details below.

# formatter

Formatter objects configure the final order, structure, and contents of the log message.

```python
import logging

# create logger
logger = logging.getLogger('simple_example')

# create console handler
ch = logging.StreamHandler()

# create formatter
formatter = logging.Formatter('%(asctime)s - %(name)s - %(levelname)s - %(message)s')

# add formatter to ch
ch.setFormatter(formatter)

# add ch to logger
logger.addHandler(ch)

# 'application' code
logger.critical('critical message')
```
    2005-03-19 15:10:26,773 - simple_example - CRITICAL - critical message
