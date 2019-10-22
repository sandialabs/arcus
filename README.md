# ![Arcus](resources/images/icon_64x64.png) Arcus

[![Nuget (with prereleases)](https://img.shields.io/nuget/vpre/Arcus?logo=nuget)](https://www.nuget.org/packages/Arcus/)
[![Documentation on ReadTheDocs](https://img.shields.io/badge/Read%20the%20Docs-Arcus-lightgrey?logo=read%20the%20docs)](https://arcus.readthedocs.io)
![Apache 2.0 license](https://img.shields.io/github/license/sandialabs/arcus?logo=apache)
![.NetStandard 1.3](https://img.shields.io/badge/targets-.NETStandard%201.3-5C2D91?logo=.net)
[![Join the chat at https://gitter.im/sandialabs/Arcus](https://badges.gitter.im/sandialabs/Arcus.svg)](https://gitter.im/sandialabs/Arcus?utm_source=badge&utm_medium=badge&utm_campaign=pr-badge&utm_content=badge)

Arcus is a C# manipulation library for calculating, parsing, formatting, converting, and comparing both IPv4 and IPv6 addresses and subnets. It accounts for 128-bit numbers on 32-bit platforms.

## Getting Started

The latest stable release of Arcus is [available on NuGet](https://www.nuget.org/packages/Arcus/).

You can also download the source source code and compile it in C#, thus creating an dll file.

An example of code usage can be found later in the [Usage](https://github.com/sandialabs/Arcus#usage) section.

## Usage

At its heart Arcus is split amongst five separate interdependent units. *Types*, *Comparers*, *Converters*, *Math*, and *Utilities*.

### Types

#### `Subnet`

An IPv4 or IPv6 subnetwork representation - the work horse and original reason for the Arcus library. Outside the concept of the `Subnet` object, most everything else in Arcus is auxiliary and exists only in support of making this once facet work. That’s not to say that the remaining pieces of the Arcus library aren’t useful, on the contrary their utility can benefit a developer greatly. That said, once the `Subnet` is mastered the rest of Arcus should follow through nicely.

A `Subnet` may be instantiated in several ways:

To the most common ways to create a subnet is to construct it via a high and low `IPAddress` call the constructor `Subnet(IPAddress primary,  IPAddress secondary)` This construct the smallest possible subnet that would contain both IP addresses typically the address specified are the Network and Broadcast addresses (lower and higher bounds) but this is not necessary. Addresses *MUST* be the same address family (either Internetwork or InternetworkV6)

It is also possible to create a `Subnet` from an `IPAddress` and an `integer` based *route prefix*. Eg: `Subnet(IPAddress ipAddress, int routingPrefix)`

Likewise it may be desired to statically parse a subnet string with `Subnet. Parse(string input)` or it’s safe equivalent of ` bool Subnet.TryParse(string input,  out Subnet subnet)`. For example, one could safely parse the `string` "192.168.1.0/16" via
```
Subnet subnet;
var success = Subnet.TryParse("192.168.1.0/16",  out subnet)
```

#### `IPAddressRange`

A basic implementation of a IIPAddressRange used to represent an inclusive range of arbitrary IP Addresses of the same address family. It isn't restricted like a `Subnet` is, allowing for non-power of two sizes, but because of this it's functionality is likewise limited.

### Comparers

The *Comparers* package contains useful Comparer objects for comparing properties of IP Addresses and IP Address composite objects

 * `DefaultAddressFamilyComparer` - A comparer that compares address families. Most frequently `Internetwork` (IPv4) and `InternetworkV6` (IPv6)
 * `DefaultIPAddressComparer` - A comparer for `IPAddress` objects
 * `DefaultIPAddressRangeComparer` - A comparer for `IIPAddressRange`. Compares such that lower order ranges are less that higher order ranges accounting for size at matching range starts

### Converters

The *Converters* package is a package of static utility classes for converting one type into another type

#### `IPAddressConverters`

Static utility class containing conversion methods for converting `IPAddress` objects into something else

### Math

The *Math* package is a package of static utility classes for doing computational mathematics on objects

#### `IPAddressMath`

In some cases the C# `IPAddress` object doesn't go far enough with what you can do with it mathematically, this static utility class containing mathematical methods to fill in the gaps.

##### Incrementing and Decrementing

Incrementing and Decrementing an `IPAddress` is as easy as a call to the `IPAddress Increment(this IPAddress ipAddress, long delta = 1)` method

Incrementing by one is a simple call to the extension method

```
var address = IPAddress.Parse(192.168.1.1);
var result = address.Increment();   // result is 192.168.1.2
```

Decrementing is just as simple 

```
var address = IPAddress.Parse(192.168.1.1);
var result = address.Increment(-2);   // result is 192.168.0.0
```

*Overflow* and *Underflow* conditions will result in an `InvalidOperationException`

##### Equality

Equality may also be tested via a host of equality extension methods

 * `bool IsEqualTo(this IPAddress alpha, IPAddress beta)`
 * `bool IsGreaterThan(this IPAddress alpha, IPAddress beta)`
 * `bool IsGreaterThanOrEqualTo(this IPAddress alpha, IPAddress beta)`
 * `bool IsLessThan(this IPAddress alpha, IPAddress beta)`
 * `bool IsLessThanOrEqualTo(this IPAddress alpha, IPAddress beta)`


### Utilities

The *Utilities* package contains static classes for miscellaneous operations on specific types

#### `IPAddressUtilities`

Static utility class containing miscellaneous operations for `IPAddress` objects

##### Address Family Detection

A couple of extension methods were created to quickly determine the address family of an IP Address. To determine if an address is IPv4 use `bool IsIPv4(this IPAddress ipAddress)`, likewise `bool IsIPv6(this IPAddress ipAddress)` can be used to test for IPv6.


##### Parsing

It is possible to parse an `IPAddress` from a hexadecimal string into either an IPv4 of IPv6 address using the `IPAddress ParseFromHexString(string input, AddressFamily addressFamily)` method. Likewise it can be done safely with `bool TryParseFromHexString(string input, AddressFamily addressFamily, out IPAddress address)`

Similarly, conversion may be done from an octal string by using `bool TryParseIgnoreOctalInIPv4(string input, out IPAddress address)` or even a `BigInteger` by way of `bool TryParse(BigInteger input, AddressFamily addressFamily, out IPAddress address)`


#### `SubnetUtilities`

Static utility class containing miscellaneous operations for `Subnet` objects that didn't make sense to put on the object itself.

Given two arbitrary IP Addresses of the same family it may be desired to calculate the fewest consecuitive subnetes that would hold the inclusive range between them. For example

```
    SubnetUtilities.FewestConsecutiveSubnetsFor(IPAddress.Parse("128.64.20.3"), IPAddress.Parse(128.64.20.12"))
```

would return an `Enumerable` containing the subnets "128.64.20.3/32", "128.64.20.4/30", "128.64.20.8/30", "128.64.20.12/32"

## Built With

* [Gulliver](https://github.com/sandialabs/gulliver) - A self created library that helped us keep our bits and byts in order
* [NuGet](https://www.nuget.org/) - Dependency Management
* [JetBrains.Annotations](https://www.jetbrains.com/help/resharper/10.0/Code_Analysis__Code_Annotations.html) - Used to keep developers honest
* [moq](https://github.com/moq/moq4/wiki) - Fake it until you make it!
* [Stackoverflow](https://stackoverflow.com/) - Because who really remembers how to code
* [xUnit.net](https://xunit.net/) - Testing, testing, 1, 2, 3...

## Versioning

We use [SemVer](http://semver.org/) for versioning.

## Primary Authors and Contributors

* **Robert H. Engelhardt** - *Primary Developer, Source of Ideas Good and Bad* - [@rheone](https://twitter.com/rheone)
* **Andrew Steele** - *Review and Suggestions* - [@ahsteele](https://twitter.com/ahsteele)
* **Nick Bachicha** - *Git Wrangler and DevOps Extraordinaire* - [@nicksterx](https://twitter.com/nicksterx)

## Copyright

> Copyright 2018 National Technology & Engineering Solutions of Sandia, LLC (NTESS). Under the terms of Contract DE-NA0003525 with NTESS, the U.S. Government retains certain rights in this software.


## License

>   Licensed under the Apache License, Version 2.0 (the "License");
 >   you may not use this file except in compliance with the License.
 >   You may obtain a copy of the License at
 >
 >       http://www.apache.org/licenses/LICENSE-2.0
 >
 >   Unless required by applicable law or agreed to in writing, software
 >   distributed under the License is distributed on an "AS IS" BASIS,
 >   WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
 >   See the License for the specific language governing permissions and
 >   limitations under the License.
