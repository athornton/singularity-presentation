:css: css/talk.css

.. That's the light-background version.

.. Commenting out :css: css/talk_dark.css

..  Swap that in if you want the dark-background version

----

:title: Singularity Containerization
:author: Adam Thornton

Singularity: A New Kind Of [*]_ Containerization
################################################

.. image:: images/Singularity-logo.svg

Adam Thornton
=============

Speaking As A Private Individual
================================

athornton@gmail.com

.. [*] Not really.

----

.. role:: raw-role(raw)
    :format: html

.. role:: strike
    :class: strike

What's the Point?
#################

Singularity is designed for situations, particularly HPC environments,
where you do not have "trusted users running trusted containers."

In particular, you want "untrusted users running untrusted
containers."

This is usually expressed as "no way you are letting Docker near my HPC
environment."

----


Docker's Security Model
#######################

.. image:: images/Dumpsterfire.gif

.. note::

 Thanks to Abbey Yacoe for putting together the gif.

----

Slightly More Detail
####################

Docker (typically) runs privileged, and it's not too hard to engineer
root-in-the-container becoming root-outside-the-container.

This does tend to make security people twitchy, especially in
traditional shared-server HPC models.

Singularity attempts to address this.

----

What Singularity Does
#####################

Runs a "container" where the owning user is the owning user and where the
network is the system network context.

* The filesystem and the process tree are apparently rooted in the
  container context.

* So, kind of like Docker without the network and UID namespaces.

----

Um, isn't that a chroot?
########################

Yes, but there's also a PID namespace.  So PID 1 in the Singularity
container isn't PID 1 outside.

----

Also magic mounts
#################

Singularity automatically bind-mounts ``$HOME``, ``/tmp``, and ``/var/tmp``.

And can bind-mount whatever else you tell it to.

----

But...privilege?
################

Singularity has three modes:

* User namespace.  No bind mounts, ephemeral, doesn't mount images.
  Effectively, pretty much useless.

* Capabilities.  Obviously the way to do it, also a hard problem and
  sysadmins don't understand them, so mostly a future research topic.

* Or good old setuid binaries for mounting the image container, for
  setting up the namespaces, and for bind-mounting local disks into the
  container.

.. image:: images/Picard-facepalm.jpg

* Well, OK, still better than Docker.

----

Singularity Images
##################

Singularity build specification file

* Called ``Singularity``

* That's not confusing AT ALL.

* Pretty much like a ``Dockerfile`` although the verb names are
  different.

----

Docker Image support
####################

Singularity can import Docker images.

This actually is a pretty cool tool for manipulating Docker image layers
without needing to install Docker, if that's a thing you want to do.

Can save images to any of several formats, but mostly seems to do
``.img`` "Singularity Images" which are just filesystem images mounted
via loopback.  This actually can increase performance if your typical
workload has many small writes, since you're just seeking in a file and
your IO requests get batched up before going to an actual device.

----

Mutability
##########

Singularity does provide for a mode where you have an immutable
container that you overlay with a mutable layer, so you can make changes
that are then discarded after you're done.  That plus bind mounts to
save your results gives you something that is pretty much equivalent to
a Docker container you run from an image and do not save when you're
done.

----

Summary
#######

Singularity is a chroot, running from a loop-mounted image, with some
bind mounts, and a PID namespace.  It uses setuid binaries to accomplish
this, and the loop-mounted image can be a Docker image.

----

Is it useful?
#############

Sure, I guess, if what you want to do is run Docker images without using
Docker.

* The use-case of an HPC environment with user shell access but *Docker?
  Aw hell no!* is a real thing, and Singularity **does** address that.

* Nevertheless: in my opinion you're better off finding a managed
  Kubernetes provider and running your workload in containers there.

* The lack of network namespacing makes Singularity less than useful for
  multi-component applications.

* However, it *is* good for wrapping up horrible scientific software
  with terrible ancient dependencies so it can run your analysis without
  destroying the rest of the system.

* Which, to be fair, is pretty much the design goal.

----

Snark
#####

Did you think Docker was a lot of hype for something that's a kind of
crappy CLI wrapped around namespaces and cgroups?  Then you're gonna
really hate Singularity.  It's a chroot with a PID namespace and some
bind-mounts.

What's a Singularity?  A black hole, of course:

* ...there's really nothing there, and...

* ...it kinda sucks.
