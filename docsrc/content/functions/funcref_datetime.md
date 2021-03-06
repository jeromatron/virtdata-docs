---
date: 2018-06-18
title: datetime
weight: 75
menu:
  main:
    parent: Function Reference
    identifier: reference-datetime
    weight: 52
---
Functions in this category know about times and dates,
datetimes, seconds or millisecond epoch times, and so forth.

Some of the function in this category are designed to
allow testing of UUID types which were designed to avoid
determinism. This makes it possible to test systems which
depend on UUIDs but which require determinism in test data.

## StartingEpochMillis

This function sets the minimum long value to the equivalent
unix epoch time in milliseconds. It simply adds the input
value to this base value as determined by the provided
time specifier. It wraps any overflow within this range as well.

- long -> StartingEpochMillis(String: baseTimeSpec) -> long
  - *ex:* `StartingEpochMillis('2017-01-01 23:59:59')` - *add the millisecond epoch time of 2017-01-01 23:59:59 to all input values*

## ToDate

Convert the input value to a {@code Date}

- long -> ToDate(int: spacing, int: repeat_count) -> java.util.Date
- long -> ToDate(int: spacing) -> java.util.Date
- long -> ToDate() -> java.util.Date

## ToDateTime

Convert the input value to a {@code org.joda.time.DateTime}

- long -> ToDateTime(int: spacing, int: repeat_count) -> org.joda.time.DateTime
- long -> ToDateTime(String: spacing) -> org.joda.time.DateTime
- long -> ToDateTime() -> org.joda.time.DateTime

## ToEpochTimeUUID

Converts a long UTC timestamp in epoch millis form into a Version 1 TimeUUID
according to <a href="https://www.ietf.org/rfc/rfc4122.txt">RFC 4122</a>.
This means that only one unique value for a timeuuid can be generated for
each epoch milli value, even though version 1 TimeUUIDs can normally represent
up to 10000 distinct values per millisecond. If you need to access this
level of resolution for testing purposes, use ToFinestTimeUUID
instead. This method is to support simple mapping to natural timestamps
as we often find in the real world.

For the variants that have a String argument in the constructor, this is
a parsable datetime that is used as the base time for all produced values.
Setting this allows you to set the start of the time range for all timeuuid
values produced. All times are parsed for UTC. All time use ISO date ordering,
meaning that the most significant fields always go before the others.

The valid formats, in joda specifier form are:
<ol>
<li>yyyy-MM-dd HH:mm:ss.SSSZ, for example: 2015-02-28 23:30:15.223</li>
<li>yyyy-MM-dd HH:mm:ss, for example 2015-02-28 23:30:15</li>
<li>yyyyMMdd'T'HHmmss.SSSZ, for example: 20150228T233015.223</li>
<li>yyyyMMdd'T'HHmmssZ, for example: 20150228T233015</li>
<li>yyyy-MM-dd, for example: 2015-02-28</li>
<li>yyyyMMdd, for example: 20150228</li>
<li>yyyyMM, for example: 201502</li>
<li>yyyy, for example: 2015</li>
</ol>

- long -> ToEpochTimeUUID() -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a per-host node and empty clock data.
The node and clock components are seeded from network interface data. In this case,
the clock data is not seeded uniquely.
  - *ex:* `ToEpochTimeUUID()` - *basetime 0, computed node data, empty clock data*
- long -> ToEpochTimeUUID(long: node) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and empty clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToEpochTimeUUID(5234)` - *basetime 0, specified node data (5234), empty clock data*
- long -> ToEpochTimeUUID(long: node, long: clock) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and specific clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToEpochTimeUUID(31,337)` - *basetime 0, specified node data (31) and clock data (337)*
- long -> ToEpochTimeUUID(String: baseSpec) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a per-host node and empty clock data.
The node and clock components are seeded from network interface data. In this case,
the clock data is not seeded uniquely.

  - *ex:* `ToEpochTimeUUID('2017-01-01T23:59:59')` - *specified basetime, computed node data, empty clock data*
- long -> ToEpochTimeUUID(String: baseSpec, long: node) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and empty clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToEpochTimeUUID('2012',12345)` - *basetime at start if 2012, with node data 12345, empty clock data*
- long -> ToEpochTimeUUID(String: baseSpec, long: node, long: clock) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and specific clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToEpochTimeUUID('20171231T1015.243',123,456)` - *ms basetime, specified node and clock data*

## ToFinestTimeUUID

Converts a count of 100ns intervals from 1582 Julian to a Type1 TimeUUID
according to <a href="https://www.ietf.org/rfc/rfc4122.txt">RFC 4122</a>.
This allows you to access the finest unit of resolution for the
purposes of simulating a large set of unique timeuuid values. This offers
10000 times more unique values per ms than ToEpochTimeUUID.

For the variants that have a String argument in the constructor, this is
a parsable datetime that is used as the base time for all produced values.
Setting this allows you to set the start of the time range for all timeuuid
values produced. All times are parsed for UTC. All time use ISO date ordering,
meaning that the most significant fields always go before the others.

The valid formats, in joda specifier form are:

<ol>
<li>yyyy-MM-dd HH:mm:ss.SSSZ, for example: 2015-02-28 23:30:15.223</li>
<li>yyyy-MM-dd HH:mm:ss, for example 2015-02-28 23:30:15</li>
<li>yyyyMMdd'T'HHmmss.SSSZ, for example: 20150228T233015.223</li>
<li>yyyyMMdd'T'HHmmssZ, for example: 20150228T233015</li>
<li>yyyy-MM-dd, for example: 2015-02-28</li>
<li>yyyyMMdd, for example: 20150228</li>
<li>yyyyMM, for example: 201502</li>
<li>yyyy, for example: 2015</li>
</ol>

- long -> ToFinestTimeUUID() -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a per-host node and empty clock data.
The node and clock components are seeded from network interface data. In this case,
the clock data is not seeded uniquely.
  - *ex:* `ToFinestTimeUUID()` - *basetime 0, computed node data, empty clock data*
- long -> ToFinestTimeUUID(long: node) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and empty clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToFinestTimeUUID(5234)` - *basetime 0, specified node data (5234), empty clock data*
- long -> ToFinestTimeUUID(long: node, long: clock) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and specific clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToFinestTimeUUID(31,337)` - *basetime 0, specified node data (31) and clock data (337)*
- long -> ToFinestTimeUUID(String: baseTimeSpec) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a per-host node and empty clock data.
The node and clock components are seeded from network interface data. In this case,
the clock data is not seeded uniquely.

  - *ex:* `ToFinestTimeUUID('2017-01-01T23:59:59')` - *specified basetime, computed node data, empty clock data*
- long -> ToFinestTimeUUID(String: baseTimeSpec, long: node) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and empty clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToFinestTimeUUID('2012',12345)` - *basetime at start if 2012, with node data 12345, empty clock data*
- long -> ToFinestTimeUUID(String: baseTimeSpec, long: node, long: clock) -> java.util.UUID
  - *notes:* Create version 1 timeuuids with a specific static node and specific clock data.
This is useful for testing so that you can know that values are verifiable, even though
in non-testing practice, you would rely on some form of entropy per-system to provide
more practical dispersion of values over reboots, etc.

  - *ex:* `ToFinestTimeUUID('20171231T1015.243',123,456)` - *ms basetime, specified node and clock data*

## ToJodaDateTime

Convert the input value to a {@code org.joda.time.DateTime}

- long -> ToJodaDateTime(int: spacing, int: repeat_count) -> org.joda.time.DateTime
- long -> ToJodaDateTime(String: spacing) -> org.joda.time.DateTime
- long -> ToJodaDateTime() -> org.joda.time.DateTime

