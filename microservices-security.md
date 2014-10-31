%title: Microservices Security
%author: Magnus Haug

---

## Motivation
A good microservices architecture consists of a set of _simple_, _loosely coupled_
services, communicating through a _well defined protocol_.

We will look at how this encourages good security,
and how we can leverage it to secure the architecture as a whole.

---

## Agenda
* Isolation
* Communication
* Making it work

---

## Isolation

---

## Isolation
* Reduced complexity leads to fewer errors.
* More well defined interfaces, easier to reason about.
* Lock down service boundary assumptions with contracts.
* Risk matrix: Lets you invest in security where it matters

---

## Isolation: Data isolation
* SQL injection
* Resource exhaustion 

---

## Isolation: Process isolation
* Fault isolation
* Risk management

---

## Isolation: File access
* Read
* Write
* Execute
* Setuid
* Setgid
* Restricted deletion flag

How: useradd, groupadd, usermod
How: chown, chmod

---

## Isolation: User/group limits imits
* nproc: Number of processes
* nofile: Number of open file descriptors
* chroot: Directory to chroot user
* as: Max addressable address space
* memlock: Max locked memory
* nice: Min nice level
* etc..

How: /etc/limits.conf

---

## Isolation: Capabilities
* CAP\_LINUX\_IMMUTABLE: Set immutable/append flags on inodes
* CAP\_NET\_BIND\_SERICE: Bind to ports < 1024
* CAP\_SYS\_PTRACE: Ptrace arbitrary processes
* CAP\_SYS\_TIME: Alter systime
* etc.
* per-tread basis, unfortunately not inheritable in Linux

How: setcap, getcap

---

## Isolation: Cgroups
* Like capabilities, but more fine grained and powerful
* Own virtual cpu, memory space, block i/o, network

How: cgcreate, cgclassify, cgexec, /etc/cgrules.conf

---

## Isolation: Containers
* Full cgroups with firewalls, mounts, users, etc.
* Based on LXC or similar
* Separate subnets (vmware, weave), firewalls
* Separate entropy pools
* Completely separate system config and kernel?
* Monitor with cAdvisor
* Containers are not completely secure

How: lxc-create, lxc-run, /etc/lxc
How: docker create, docker run, docker commit, docker push
How: weave launch, weave run, weave attach, weave detach, weave expose

---

## Isolation: VMs
* Separate kernel
* Separate system resources
* Separate entropy pools
* (Almost) complete isolation

How: vagrant, vmware, xen

---

## Isolation: Separate server
* Resource exhaustion
* Side channel attacks

---

## Secure communication

---

## Secure communication: Virtual networks
* Minimizing external attack surface
* Not easy to automate
* Easier with docker and weave

---

## Secure communication: Certificate based auth
* Requires requests to be authenticated with a certificate
* Not so easy for external browsers

---

## Secure communication: SAML2 Assertions
* Heavyweight XML
* Feature complete
* Designed for SOAP
* Complex (vulnerable)

---

## Secure communication: OAuth 2
* Lightweight
* Mainly for authorization only
* JSON friendly, nice APIs
* Easy to set up a server (free in many PaaS)

---

## Secure communication: OpenID Connect
* Lightweight authentication framework built on top of OAuth 2
* JSON friendly, nice APIs

---

## Secure communication: XACML 3.0
* Authorization framework
* XML based, heavyweight
* More feature heavy than OAuth 2

---

## Making it work

---

## Making it work: New challenges
* Auth at scale
* Time synchronization
* Keeping every server up-to-date
* Keeping every application up-to-date
* XML attacks?
* New distributed asymmetric DDoS attacks?
* Too many new platforms and technologies?

---

## Making it work: Tools
* DependencyChecker
* Retire.js
* Unattended-upgrades
* OWASP AppSensor
* Dapper/Zipkin
* Firewalls
* Microcontainers
* Scripted environments

---

## Making it work: Simplicity focused mindset
* Keep your protocols simple
* Keep your applications simple
* Keep your infrastructure simple
* Prefer application complexity over protocol complexity 

---

## Making it work: Security focused mindset
* Monitor your applications
* Expose minimal attack surfaces
* Invest in security where it matters
* Only give applications the rights they need
* Let all applications be mutually untrusting

---

## Feedback
http://goo.gl/izr43O

---

## Links and references
Linux limits.conf: http://linux.die.net/man/5/limits.conf
Linux capabilities: http://linux.die.net/man/7/capabilities
Consumer driven contracts: http://martinfowler.com/articles/consumerDrivenContracts.html
LXC - Linux Containers: https://linuxcontainers.org/ 
Docker: https://www.docker.com/
cAdvisor: https://github.com/google/cadvisor
Weave: https://github.com/zettio/weave
Containers do not contain: http://opensource.com/business/14/7/docker-security-selinux
Dapper: http://research.google.com/pubs/pub36356.html
Zipkin: https://blog.twitter.com/2012/distributed-systems-tracing-with-zipkin
SAML vs Zipkin: http://www.mutuallyhuman.com/blog/2013/05/09/choosing-an-sso-strategy-saml-vs-oauth2/
XACML is dead: http://blogs.forrester.com/andras_cser/13-05-07-xacml_is_dead
OWASP AppSensor 2.0.1 Guide: https://www.owasp.org/images/0/02/Owasp-appsensor-guide-v2.pdf
Immutable environments: http://martinfowler.com/bliki/ImmutableServer.html
