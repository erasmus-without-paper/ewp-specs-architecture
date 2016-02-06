Architecture and Security
=========================

This document describes EWP Network components, establishes some common
security measures, features, data types and vocabulary, and explains how
partners communicate with themselves.

* [What is the status of this document?][statuses]
* [See the index of all other EWP Specifications][develhub]


How to join the EWP Network?
----------------------------

### Quick start

In order to join our network you'll need to:

 * Publish a valid [Discovery Manifest][discovery-api] somewhere on your
   servers.

 * Send the URL of your manifest to the EWP Registry maintainers. It will be
   stored in the EWP Partners Registry, allowing other partners to identify and
   verify your future requests.


### Implementing APIs

Having completed the two steps described above, you will become a member of the
EWP Network, and you will be able to issue requests to other EWP members
(retrieve information from **other** institutions). You will NOT however share
any of **your** data yet - you'll need to implement some more APIs to do that.

For a complete list of APIs - see the [Discovery API documentation]
[discovery-api].


Hosts, APIs and other components
--------------------------------

### HEIs

HEI is an acronym for **Higher Education Institution**. The EWP Project aims on
allowing communication between them.

![Scattered HEIs](images/diagram-step1.png)


### EWP Hosts

In order for the HEI to benefit from the EWP Project, it needs to be placed
within an EWP Host.

 * EWP Hosts are *groups of HEIs*. Host 1 **covers** institutions A, B and C,
   while institution D is **being covered** by Host 2. Institution E is not
   covered by any host within the EWP network.

 * One HEI SHOULD be covered by no more than one EWP Host.

 * In some countries, there will be only a single EWP Host per country. In
   other countries every HEI will run its own EWP Host.

![HEIs have been covered by two EWP Hosts](images/diagram-step2.png)

Now, let's say that HEI B wants to fetch some student data from the EWP
Network.

 * HEI B **knows** the ID of the student, and it knows the ID of the home
   institution of this student. So it knows which HEI it wants to fetch the
   data *from* (let's say it's HEI D).

 * HEI B asks *its own* EWP Host (Host 1) to fetch the data it needs from the
   EWP Network.

 * Host 1 **does not know** which of the EWP Hosts **covers** HEI D. It doesn't
   even know if HEI D is *being covered* in the EWP Network.

Host 1 needs the Registry Service in order to answer these questions.


<a name="registry"></a>

### The Registry

The Registry is the only centralised part of the EWP architecture. It allows
all EWP hosts to access the list of other EWP hosts. (It MAY also be used for
projects unrelated to EWP, as long as these projects have similar
architectures.)


#### How is it updated?

The registry is being updated **automatically**. It periodically reads all
the information which all EWP Hosts provide within their [Discovery Manifest
files][discovery-api], and these changes are reflected in the registry
responses.

The major advantage of such automatic updating is that the partners *do not
need* to contact the registry maintainer when they want to change some of their
Registry entries. Most changes in the registry can performed simply by updating
the manifest on **the partner's** server (and the Registry will fetch these
changes automatically).


#### Accessing the Registry

Let's continue the example use case we have started to describe earlier.

**Host 1** calls the EWP Registry Service. The Registry Service keeps track
of all the EWP Hosts and of all the HEIs covered by them. Given the Registry
response, Host 1 is now able to determine that it needs to contact Host 2 in
order to get the data.

Detailed documentation on **how** to access the Registry is described [here]
[registry-spec].

![Registry connects EWP Hosts with each other](images/diagram-step3.png)


### APIs

EWP Hosts are **not required** to implement *all* features of the EWP Network.
Host 2 covers HEI D, but this **does not necessarily mean** that Host 2 has
implemented the API needed (or specific API *version* needed) by Host 1 in
order to fetch the student data it needs.

In order to avoid unnecessary requests, The Registry Service keeps track of
all the implemented APIs too. Host 1 will be able to verify if the specific API
has been implemented, and retrieve its URL from the Registry.

It's worth noting that API implementations can be hosted across multiple
servers (which might be convenient, especially if you work with multiple
developer teams, each one of which might want to use their favourite languages
and environments.


![Registry keeps track of implemented APIs](images/diagram-step4.png)

**Side-note** (for geeks only): Technically speaking, the Registry Service is
*also* an API, and it is also embedded inside its own EWP Host, like all the
other APIs. However, Registry's *EWP Host* (not shown in a diagram above) does
not cover any HEIs and it does not implement any other APIs (except the
[Registry API][registry-spec]). So it's quite different from all other EWP
Hosts.


### Push notifications (and broadcasting)

What if HEI B wanted to **notify** HEI D about something, instead of fetching
the data from it? E.g. a student has just failed an exam at B and we suspect
that D would want to know it?

Some of the APIs are **event listener APIs**. If Host 2 wants to receive such
notifications from other hosts, it indicates that it has implemented a specific
event listener API in its Manifest file. Now, if Host 1 is able to broadcast
such notifications, then it asks the Registry which hosts have implemented the
proper listeners, and posts the proper notifications at the listener URLs.

It's worth noting that Host 1 is NOT REQUIRED to be able to broadcast such
notifications (even if all other hosts implement listeners). Implementers MAY
choose to implement only a subset of push notifications, or even not implement
them at all. You will find more information on this in the documentation of
specific event listener APIs.


Security
--------

### SSL Certificates

There are two types of certificates which all implementers must be aware of:

 * **Server certificates** are used by the Host when it **responds** to API
   requests.
 * **Client certificates** are used to **issue requests** within the EWP
   Network.

Implementers MAY use the same certificate for both purposes, but it is NOT
REQUIRED.


### Server certificates

These are just "regular" SSL certificates, bound to a domain.

All our APIs MUST be served via the HTTPS protocol and protected by a
certificate bound to their domain. This allows the clients to verify that the
responses come from the proper source.

Note, that APIs can be spread across multiple domains.


### Client certificates

Each EWP Host declares a list of certificates it will use for making requests
to other hosts. It does so by including acceptable domain names and/or specific
X.509 certificates in its Manifest file (review the Manifest's XSD file for
details). This list will often contain just one or two domain entries, but it
MAY also contain an unlimited number of **self-signed** certificates.

This setup has the following advantages:

 * During development stages, it allows developers to generate their own
   certificates and install them in their browsers for debugging purposes.

 * In production environments, it facilitates the usage of EWP Network, its
   Registry and Manifest files in **other** projects (unrelated to EWP). Such
   projects will most probably serve their APIs (and clients) on other domains
   (so we need to support more than a single domain or certificate).

 * Using domain names allows client certificates to be easily replaced by newer
   versions when they expire. The manifest will not need to be updated.

The list of allowed domains and certificates in use can be acquired from the
EWP Registry.


### Should I verify *all* requests?

No. Just some.

Every API defines its own security requirements. In most cases it *will* be
REQUIRED for the EWP Host to verify its requester, but in some other cases
(e.g. the Discovery Manifest API) it will be the opposite - some requests MUST
be allowed to be performed by **anonymous** requesters (with no client
certificate).

When verifying client certificates, you MUST match them against all the domains
and certificates published in the EWP Registry (review the Registry's XSD for
details).


### Should I verify *all* responses?

Yes.

It is RECOMMENDED for all EWP Clients to verify the SSL server certificates
when retrieving responses from other EWP Hosts. Use the "regular" SSL
verification - you do not need to compare certificates with the lists
provided by the Registry as EWP Servers do - you simply need to check if the
server's certificate was signed by a trusted CA.


### Example Security Policies

Every API specifies its own security policy. Here are some examples of common
policies.

 * **Anonymous access**: The request can be made by anyone. The client does not
   need to use any SSL certificate.

 * **Access to anyone from within the EWP Network**: Client certificate is
   required. The server needs to verify if the client's request was signed by
   *any* of the certificates *or* domains listed in the EWP Registry.

 * **Access to a single EWP Host which covers a specific HEI**: Requests
   MUST be signed by a certificate of a single, specific EWP Host. Other EWP
   Hosts MUST NOT be able to access the resource. (This will probably be the
   most common policy across EWP APIs, as the project focuses on exchanging
   data between HEIs.)


<a name='backward-compatibility-rules'></a>

Backward-compatibility rules
----------------------------

All implementers and API designers MUST follow the following basic rules when
designing, developing and accessing API methods:

 * Adding a new **optional** element to an XML file is backward-compatible.
   Client implementers MUST ignore unknown elements. If an element has not
   existed in version `1.0.0`, but you want it to exist in the next version,
   then you can tag the next version as `1.1.0`.

 * Adding a new **required** element to an XML file is NOT backward-compatible.
   All hosts which implement this API will need to be upgraded, and designers
   MUST bump the API version to `2.0.0`.

 * Changing an existing element is *usually* NOT backward-compatible. For
   example, if the element contained an integer in version `1.0.0`, but API
   designer wants it to be able to contain a float from now on, then he MUST
   increase the version number to `2.0.0`.

 * Adding an **optional** parameter to the API method interface is
   backward-compatible, as long as the default value for the newly added
   element doesn't change the method output which the older clients expect to
   receive.

 * It is agreed that after an API is deployed onto production systems, all
   changes to this API SHOULD be kept backward-compatible - new major versions
   of such APIs SHOULD NOT be released. However, such APIs MAY become
   [deprecated][statuses] (and new APIs MAY be released in their place).


[discovery-api]: https://github.com/erasmus-without-paper/ewp-specs-api-discovery/blob/stable-v1/README.md
[develhub]: http://developers.erasmuswithoutpaper.eu/
[statuses]: https://github.com/erasmus-without-paper/ewp-specs-management/blob/stable-v1/README.md#statuses
[registry-spec]: https://github.com/erasmus-without-paper/ewp-specs-api-registry/blob/master/README.md
