
Katzenpost Handbook
*******************

| David Stainton

Version 0

.. rubric:: Abstract

This document describes how to use the Katzenpost Mix Network software
system and details deployment strategies for systems administrators.

.. contents:: :local:


Introduction
============

Thank you for interest in Katzenpost! Katzenpost can be used as a
message oriented transport for a variety of applications and is in no
way limited to the e-mail use case. Other possible applications of
Katzenpost include but are not limited to: instant messenger
applications such as Signal and Whatsapp, crypto currency transaction
transport, bulletin board systems, file sharing and so forth.

The Katzenpost system has four component categories:

* public key infrastructure
* mixes
* providers
* clients

This handbook will describe how to use and deploy each of these.
The build instructions in this handbook assume that you have a proper
golang environment with at least golang 1.9 or later AND the git
revision control system commandline installed.


Katzenpost Mix Network Public Key Infrastructure
================================================

Overview
--------

Currently Katzenpost has one PKI system that is ready for deployment;
the non-voting Directory Authority. Whether or not this should be used
on a production system depends on your threat model. This is
essentially a single point of failure. If this PKI system becomes
compromised by an adversary it's game over for anonymity and security
guarantees.

The Katzenpost voting Directory Authority system is a replacement for
the non-voting Directory Authority and is actively being developed.
However it's votiing protocol is NOT byzantine fault tolerant.
Therefore a Directory Authority server which is participating in the
voting protocol can easily perform a denial of service attack for each
voting round. This would cause the mix network to become totally
unusable.

Future development efforts will include designing and implementing one
or more byzantine fault tolerant PKI systems for Katzenpost.

All Katzenpost PKI systems have two essential components:

* a client library
* server infrastructure

Furthermore this client library has two types of users, namely mixes
and clients. That is, mixes must use the library to upload/download
their mix descriptors and clients use the library to download a
network consensus document so that they can route messages through the
mix network.


Building Katzenpost Components with Dependency Vendoring
--------------------------------------------------------

We recommend using our golang vendoring system to perform the builds.

0. Acquire a recent version of dep: https://github.com/golang/dep

1. Clone the Katzenpost daemons repository::

   mkdir $GOPATH/github.com/katzenpost
   git clone https://github.com/katzenpost/daemons.git
   

2. Fetch the Katzenpost vendored dependencies::

   cd $GOPATH/github.com/katzenpost/daemons
   dep ensure

3. Build the binaries::

   cd authority/nonvoting; go build
   cd server; go build
   cd mailproxy; go build


Building The Non-voting Directory Authority
-------------------------------------------

The easiest way to build the nonvoting Authority server is with
this single commandline::

   go get github.com/katzenpost/daemons/authority/nonvoting

However you can of course use git to clone all of our git
repositories and dependencies. You may then build the
nonvoing authority as follows::

   cd $GOPATH/github.com/katzenpost/daemons/authority/nonvoting
   go build

Neither of these build strategies is ideal because the latest
versions of any of our software dependencies may make breaking
changes. We therefore recommend using our golang vendoring system
to perform the build as described above.


Using The Non-voting Directory Authority
----------------------------------------

The non-voting authority has the following commandline usage::

   ./nonvoting --help
   Usage of ./nonvoting:
     -f string
           Path to the authority config file. (default "katzenpost-authority.toml")
     -g    Generate the keys and exit immediately.
