# Doing REST APIs Right
REST APIs are supposed to be simple! I wonder, why doing it right often have resulted long and heated discussions by fellow software developers?

If it is simple, what is there to argue about? Technically REST is not a specification, but a set of constraints and principles for building web services APIs using HTTP protocol. So there is a variety of options to achieve similar functionality.

In this article we will discuss designing asynchronous REST APIs. We will look into the general reccomendations and suggest little tweaks  for making things more clean and simple.

We will be building APIs for fictional image text recognition service, so our example look more natural.

# Asynchronous APIs General Practice
