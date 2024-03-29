Release notes
=============

This document describes all the changes made to the *Architecture and Common
Datatypes* document, starting from its first released version.


1.14.0
------

* Extended description of statistics.


1.13.0
------

* Added chapter about statistics endpoints.


1.12.0
------

* Added admin-provider element to common types.


1.11.0
------

* Added chapter on identifying callers and receivers.
* Imposed 1 to 1 communication in EWP.
* Added info about EQL level code for short cycle mobilities.
* Fixed #30 - clients are required to send CNRs for every API.
* Allowed unofficial "NS" code for "native speaker" language level.
* Added more details on CNR implementation and error handling.
* Added preference for HTTP 401 when client is not authenticated.


1.10.0
------

* New `simpleType` added to `common-types.xsd`: `Sha256Hex`.


1.9.0
-----

* Added details on error handling:

  - Described when to use the `user-message` field (on possible edit
    conflicts).
  - Described when to encrypt error responses (when the client requests the
    responses to be encrypted).

* Described the index-pulling practice.

* Spelling fixes (efq->eqf), formatting fixes, updated links.


1.8.0
-----

* Removed the *Security and Authentication* section. The contents of this
  section were moved to separate documents
  ([details](https://github.com/erasmus-without-paper/ewp-specs-architecture/issues/21)).

  * As a result, renamed the document to **Architecture and Common Datatypes**
    (previous name was **Architecture and Security**).

* Added a detailed explanation of the master-slave model in context of CNRs.

* Explained how to handle deleted entities via CNR.


1.7.0
-----

* Introduced `<success-user-message>` element to `common-types.xsd`. (Currently
  it is used in Outgoing Mobilities API only, but we believe that more APIs
  might want to reuse it.)


1.6.0
-----

* Updated structure of the document (changed names and indentation of some
  chapters).

* Added **a detailed chapter explaining identifiers and codes** (surrogate and
  natural keys). It defines how they should be compared with each other (in
  particular, comparison should be case-sensitive), what is the recommended
  type for the values
  ([UUIDs](https://github.com/erasmus-without-paper/general-issues/issues/10#issuecomment-280311729))
  and what restrictions these values must meet (discuss
  [here](https://github.com/erasmus-without-paper/general-issues/issues/23)).

* Added recommendations in regard to **referential integrity** and explaining
  how "old data" should be kept (discuss
  [here](https://github.com/erasmus-without-paper/general-issues/issues/20)).

* Expanded the chapter about **broadcasting changes and CNR APIs**. Among other
  things, it now contains separate recommendations for both the server and the
  client (discuss
  [here](https://github.com/erasmus-without-paper/ewp-specs-architecture/issues/19)).

* Expanded XSD annotations in regard to a possible uses of `xml:lang`'s [script
  subtag](https://en.wikipedia.org/wiki/ISO_15924).

* `minOccurs` and `maxOccurs` are now provided explicitly
  ([why?](https://github.com/erasmus-without-paper/general-issues/issues/22)).

* Typos and other minor fixes.


1.5.0
-----

* Added `<user-message>` element to `<error-response>`.


1.4.0
-----

* More simple data types added to `common-types.xsd`: `UUID`, `EqfLevel`,
  `CefrLevel`, `Gender`.
* Added a note in `common-types.xsd` explaining why we keep it in the
  *Architecture and Security* document (this was the name of this document at
  that point).


1.3.0
-----

* **New features:** Common-types added: `HTTP`, `HTTPWithOptionalLang`,
  `CountryCode`.
* Added a warning for about complexities of `xml:lang` (see
  [here](https://github.com/erasmus-without-paper/ewp-specs-architecture/issues/11)).
* Clarified that some of the guidelines are only recommendations, not strict
  requirements.


1.2.3
-----

* Added the description of the development environment.


1.2.2
-----

* Only some minor changes in formatting and vocabulary.


1.2.1
-----

* Fixed some inconsistencies in the vocabulary.

* A more appropriate definition of *EWP Host* was introduced, along with some
  more complex examples of EWP Network topologies. Images were updated to
  describe the example better.

* Described some of the complexities of the A1 architecture in detail (as
  discussed in
  [this thread](https://github.com/erasmus-without-paper/ewp-specs-api-echo/issues/3)).

* Removed an (obsolete) paragraph saying that once an API is deployed, no new
  major version of this API should be released. This has been allowed [for a
  while now](https://github.com/erasmus-without-paper/ewp-specs-architecture/issues/6).

* Updated links and references. Fixed some issues with formatting.


1.2.0
-----

* `<error-response>` element has been moved to the
  [common-types.xsd](common-types.xsd)  schema. There seemed to be no reason to
  keep it in a separate namespace.


1.1.1
-----

* Changes in [common-types.xsd](common-types.xsd).


1.1.0
-----

* Error handling rules and `error-response.xsd` schema were introduced
  ([details](https://github.com/erasmus-without-paper/ewp-specs-architecture/issues/7)).


1.0.1
-----

* Fixed links and XML namespaces
  ([details](https://github.com/erasmus-without-paper/general-issues/issues/4)).

* Got rid of the misleading "request signing" term
  ([details](https://github.com/erasmus-without-paper/ewp-specs-api-echo/issues/1)).

* Added further clarification on server and client certificates.


1.0.0
-----

Initial release. Reviewed
[here](https://github.com/erasmus-without-paper/ewp-specs-architecture/pull/1/files)
and initially accepted during the
[meeting](https://github.com/erasmus-without-paper/general-issues/issues/3)
on the 5th of February 2016.
