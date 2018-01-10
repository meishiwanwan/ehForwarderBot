Slave Channels
==============

Slave channel is more like a wrap over an API of IM,
it encloses messages from the IM into appropriate
objects and deliver it to the master channel.

Methods to be implemented
-------------------------

Below is a list of methods that are required to be 
implemented by the slave channel.

* :meth:`.EFBChannel.get_chat_picture`
* :meth:`.EFBChannel.get_chat`
* :meth:`.EFBChannel.get_chats`
* :meth:`.EFBChannel.poll`
* :meth:`.EFBChannel.send_message`
* :meth:`.EFBChannel.send_status`

Extra functions
---------------

Slave channels can offer more functions than what EFB
requires, such as creating groups, search for friends, 
etc, via *extra functions*.

Such functiones are accessed by the user in a CLI-like
style. An "extra function" method should only take one
string parameter aside from ``self``, and wrap it with 
:meth:`~ehforwarderbot.utils.extra` decorator. The extra decorator 
takes 2 arguments: ``name`` — a short name of the function, 
and ``desc`` — a description of the function and its usage.

``desc`` should describe what the function does and how
to use it. It's more like the help text for an CLI program. 
Since method of calling an extra function depends on the 
implementation of the master channel, you should use 
``"{function_name}"`` as the function name in ``desc``, 
and master channel will replace it with respective name
depend on their implementation.

The method should in the end return a string, which will 
be shown to the user as its result. Depending on the 
functionality of the function, it may be just a simple 
success message, or a long chunk of results.

The callable should not raise any exception to its caller.
Any exceptions occured within should be ``expect``ed and
processed.

Callable name of extra functions has a more strict standard 
than a normal Python 3 identifier name, for compatibility 
reason. An extra function callable name should:

* be case sensitive
* include only upper and lower-case letters, digits, and underscore.
* starts only with a upper or lower-case letter.
* be in a length between 1 and 20 inclusive
* *be as short and concise as possible, but keep understandable*

In RegEx, it's can be expressed as::

    ^[A-Za-z][A-Za-z0-9_]{0,19}$

.. admonition:: Example
    :class: tip

    .. code-block:: python
    
        @extra(name="Echo",
               desc="Return back the same string from input.\n"
                    "Usage:\n"
                    "    {function_name} text")
        def echo(self, arguments: str = "") -> str:
            return arguments

Message commands
----------------

Message commands are usually sent by slave channels so that
users can respond to certain messages that has specific 
required actions.

Possible cases when message commands could be useful.

* Add as friends when a contact card is sent.
* Accept or decline when a friend request is sent
* Vote to a voting message
* Like / thumb up to a message if applicable

A message can be attached with a ``list`` of commands, in 
which each of them has:

* a human-friendly name,
* a callable name,
* a ``list`` of positional arguments, and
* a ``dict`` of keyword arguments

When a user triggered the button, the corresponding method
of your channel will be called with provided arguments.

Note that all such methods MUST return a ``str`` as a 
respond to the action from user, and they should NOT raise
any exception to its caller. Any exceptions occured within 
should be "expect"ed and processed.
