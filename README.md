# Periscope((api))Notation
_The structured filter notation for API queries._

**Author:** [Gary Clift](https://github.com/gclift)

**Financial Support:** [Tribe Alpha Corp](https://tribealpha.com)

**License:** [Creative Commons Attribution-Share Alike (BY-SA)](https://creativecommons.org/licenses/by-sa/4.0/)

![Creative Commons Attribution-Share Alike](https://smartcopying.edu.au/wp-content/uploads/2020/03/image3.jpg "Creative Commons Attribution-Share Alike")

**Status:** Super Early Draft (Updated 2021-07-27)

**Roadmap::**
* Sept 1st, 2021 - First Draft
* January 1st, 2022 - First Version

**Motivation:** After consuming data from APIs for almost 20 years, I've grown tired of APIs having different filter formats for result data. We need a common way to request data from the world's ever-increasing number of API systems.

**Goals:**
* A notation that is human friendly. Easy syntax and human readability will lead to faster API adoption and fewer bugs in consuming code bases.
* A notation that is computer friendly. Computers can easily parse the filter string and filter a large set of data. A set of open source projects for different programming languages will be available for easy adoption by API architects.
* A notation that is programming language agnostic and evolutionary. A notation can work with any programming language and can evolve and adapt to the ever-changing technical world.

## Quick Start

## Basic examples
first_name((eq))john - Return all records where the first name equals John (case-insensitive)

first_name((starts))jo - Return all records where the first name starts with Jo (case-insensitive)

first_name|last_name((ends))doe - Return all records where the first and last name ends with doe (case-insensitive).

first_name((nempty)) - Return all records where the first name is not empty.

### URI GET parameter example

http://example.com/api/v01/entity?pn[]=first_name((nempty))&pn[]=last_name((eq))jones&pn[]=((limit))100&pn[]=((offset))15
* Where the first name is not empty
* AND where the last name equals jones
* return 100 records
* starting at record 15



## Definition
Periscope Notation is a structured filter notation for API queries. Periscope notation contains two (bipartite) or three (tripartite) parts depending on the operator being used to filter. The left part of the notation is always the property. The center part is always the operator. For operators who don't have an implicit value, the right part is always the value.

## Notation Parts:
### The property - Left Part
The property contains the name of the entity property being queried. An example would be the "First Name" of a User entity. If the entity property is using snake case, the left part would be "first_name". Multiple properties can be used as a boolean "OR" and use the same operator and value by delimiting the properties with the pipe delimiter. For example, if searching for the first name "John" or the last name "John" the property would be "first_name|last_name".  See the rules below for more information on the property part. A couple operators have an implicit property name and is not used. See "limit" for an example.

### The operator - Middle Part
The operator defines how the property is filtered. Most operators will require a value, which is found in the right part. An example of an operator that requires a value is the equals operator represented by "eq". Some operators have an implied value and the right part is not required. An example of an operator with an implied value is the not empty operator. See the rules below for more information on the operator part.

### The value - Right Part
The value part of the notation represents the value of the entity's property being filtered. If the filter is to find the first name of a user named John, then the value would be the string john. See the rules below for more information on the value part.

## Periscope Notation Rules

### Property Rules
The case of the property is case-sensitive and the case format must match the return value format. For example, if you request first_name then the resulting data must have a property called "first_name" and not firstName or FirstName.

Multiple properties may be used and delimited by the pipe delimiter. The property "first_name|last_name" would be used if you want to search by first_name OR last_name.

The property must be in the resulting set of data returned from the API to the consumer.

### Operator Rules
There are three types of operators. The standard set operators are required by the producer APIs and must implement the standard set operators for properties that use Periscope Notation. The extended set of operators are optional operators the producer API may or may not implement. It is assumed they are not implemented unless otherwise documented. Producer APIs may include additional custom operators and must provide the documentation to the client developers.

### Standard Set of Operators:
**eq** - equals

``x((eq))y --> `x` == "y"``

**not** - not equals

``x((not))y --> `x` != "y"``

**contains** - contains

``x((contains))y --> `x` LIKE "%y%"``

**starts** - starts with

``x((starts))y --> `x` LIKE "y%"``

**ends** - ends with

``x((ends))y --> `x` LIKE "%y"``

**gt** - greater than

``x((gt))y --> `x` > "y"``

**gte** - greater than or equal to

``x((gte))y --> `x` >= "y"``

**lt** - less than

``x((lt))y --> `x` < "y"``

**lte** - less than or equal to

``x((lte))y --> `x` <= "y"``

**in** - in

``x((in))y --> `x` IN (y1, y2, y3)``

**nin** - not in

``x((nin))y --> `x` NOT IN (y1, y2, y3)``

**between** - between

``x((between))y --> `x` >= "y1" AND `x` <= "y2"``

**empty** - empty

``x((empty)) --> `x` IS NULL OR `x` == ""``

**nempty** - not empty

``x((nempty)) --> `x` IS NOT NULL AND `x` != ""``

**asc** - order by ascending values

``x((asc)) --> ORDER BY `x` ASC``

**desc** - order by descending values

``x((desc)) --> ORDER BY `x` DESC``

**offset** - The record value to start in pagination

``((offset))15 --> LIMIT 15``

**limit** - order by descending values

``((limit))100 --> LIMIT 15,100``



###Enhanced Set of Operators:
_Coming soon_

### Value Rules
The value is required for operators that do not have an implied value. The value is case-insensitive.

Multiple values can be used in one filster string and the filter would use a boolean OR to filter the data. For example, the value "john|johnathan" would search for john OR jonathan.

Complex filter strings can be used together to build a complex filter. If a property is included in more than one filter string then the filter would use the boolean OR to search for matching data. If more than one property is found in the multiple filter strings then the boolean AND will be used and only data that matches all property filters will be returned.

Some operators require two values. Multiple values for an operator are delimited with commas. See the between operator as an example.

## FAQs
**Why the name Periscope?**
The notation when using the tripartite operators looks like a submarine's periscope handle and viewport.

![Periscope by Taylor Vatem from unsplash](https://images.unsplash.com/photo-1625578525732-4078f41c567d?ixid=MnwxMjA3fDB8MHxwaG90by1wYWdlfHx8fGVufDB8fHx8&ixlib=rb-1.2.1&auto=format&fit=crop&w=150&q=80)

_Periscope photo by Taylor Vatem from unsplash_

**Can I use open and close parentheses in the query part of a URI?**

According to the RFC 3986 by the god himself, Tim Berners-Lee, the open and close parentheses are considered "sub-delims", but have no meaning in the path or query part of the URI. _Is this debatable? Yes._

## Limitations
Since the spec rules declare that if two filter strings use the same property then the filter will OR the results, it's not possible to define two different filters for the same property and AND the filter. So, first_name((eq))john + first_name((neq))johnathan cannot be represented by first_name == 'john' AND first_name != 'johnathan'.

## Examples
### Data
_Record 1_
first_name: "Tom"
last_name: "Jones"
date_created: "1970-01-01"

_Record 2_
first_name: "Thomas"
last_name: "Dolby"
date_created: "1970-01-01"

_Record 3_
first_name: "Davy"
last_name: "Jones"
date_created: "1980-01-01"

first_name((eq))tom|thomas would return records 1 and 2

last_name((starts))jone would return records 1 and 3

last_name((eq))jones + date_created((gt))1970-01-01 would return only record 3.

date_created((between))1970-01-01,1990-01-01


## Related Projects

### PHP
* Periscope Notation - PHP Parser  (coming soon)
* Periscope Notation - Doctrine Query Builder Filter  (coming soon)
* Periscope Notation - PHP Array Filter (coming soon)

### NodeJS
* Periscope Notation - NodeJS Parser (coming soon)

### Java
* Periscope Notation - Java Parser (coming soon)

## APIs Using PeriscopeNotation
* Please add known APIs using the PeriscopeNotation



