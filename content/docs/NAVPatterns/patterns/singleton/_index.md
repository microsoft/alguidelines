+++
title = "Singleton"
weight = 1090
+++
_By Bogdana Botez at Microsoft Development Center Copenhagen_

[![ ][image0]][anchor0]

**Context:** The pattern described in this article applies to Dynamics NAV only. For the general definition of the **Singleton** pattern, see for example [this link][anchor1].

**Problem**: As a C/AL developer, you need to coordinate action (through a codeunit) or store information (in a table) that is unique across the system. 

**Forces:**

* **Lost reference to centralizer:** An instance of a relevant object could attempt to centralize control or data in the system. However, once this object is no longer in scope, the reference to it is lost and cannot be retrieved for later coordination
* **Cannot rebuild the initial state:** A new instance of the same object could be created, however the last known state of the lost instance cannot be known anymore.

**Solution:** create an object which resides in memory in a single copy (instance). Have a way to retrieve this unique object from code. This object can either be a [**Singleton Table**][anchor2], or a [**Singleton Codeunit**][anchor3].

The implementation varies depending on the technology and language used. In object-oriented (OO) languages like C\#, C++ or Java, the **Singleton** uses static classes and class members to instantiate. There is a mechanism to ensure only one instance of the object can exist. This mechanism is many times explicit (like in OO languages) and sometimes implicit as part of the compiler or platform (as it is in NAV).

**Benefits:**

* **Centralization**: the setup information is stored in a single well-known place and easily retrievable from anywhere in the application code, by invoking a Record.GET. In case of a single-instance codeunit, any reference to it will retrieve the same instance, so the context will be preserved.
* **Persistence**: information remains even after the instance goes out of scope, because it continues to live in memory.

**Limitation:** The generic object-oriented **Singleton** pattern permits instantiation of a limited number n of objects (where usually n=1, but it can have other positive values too). However, in Dynamics NAV, the Singleton patterns are limited: n is always 1\.



[anchor0]: 0535.Singleton.png
[anchor1]: https://en.wikipedia.org/wiki/Singleton_pattern
[anchor2]: /navpatterns/1-patterns/singleton/singleton-table/
[anchor3]: /navpatterns/1-patterns/singleton/singleton-codeunit/


[image0]: 0535.Singleton.png
