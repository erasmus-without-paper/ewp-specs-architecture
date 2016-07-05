Release notes
=============

This document describes all the changes made to the *Architecture and Security*
document, starting from its first released version.


1.2.1
-----

* Fixed some inconsistencies in the vocabulary.

* A more appropriate definition of *EWP Host* was introduced, along with some
  more complex examples of EWP Network topologies. Images were updated to
  describe the example better.

* Described some of the complexities of the A1 architecture in detail (as
  discussed in [this thread]
  (https://github.com/erasmus-without-paper/ewp-specs-api-echo/issues/3)).

* Removed an (obsolete) paragraph saying that once an API is deployed, no new
  major version of this API should be released. This has been allowed [for a
  while now](https://github.com/erasmus-without-paper/ewp-specs-architecture/issues/6).

* Updated links and references. Fixed some issues with formatting.


1.2.0
-----

* `<error-response>` element has been moved to the [common-types.xsd]
  (common-types.xsd)  schema. There seemed to be no reason to keep it in a
  separate namespace.


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
  ([details] (https://github.com/erasmus-without-paper/general-issues/issues/4)).

* Got rid of the misleading "request signing" term
  ([details] (https://github.com/erasmus-without-paper/ewp-specs-api-echo/issues/1)).

* Added further clarification on server and client certificates.


1.0.0
-----

Initial release. Reviewed
[here](https://github.com/erasmus-without-paper/ewp-specs-architecture/pull/1/files)
and initially accepted during the
[meeting](https://github.com/erasmus-without-paper/general-issues/issues/3)
on the 5th of February 2016.
