# DaLTo Rationale

## Context

This project started from several observations and frustrations. I was first looking for a data representation language that would make my life easy as a developer and my users' life easier. What I had in mind was some kind of language where I could write a data schema, generate my code from it (any language... Rust, js, Scala, Haskell, ...), provide my user with a data validator and eventually some features like extracting environment variables, multiline strings, referencing data in the document, spreading your data across several files and chosing how things are merged...

Obviously I couldn't find my silver bullet and decided that what was needed wasn't a new language but some tools to leverages them. DaLTo stands for Data Language Toolkit.

## Observations

There is a proliferation of data format, here is my review of the ones I have taken a deeper look at. (Not mentionned in this list: [Hashicorp CL](https://github.com/hashicorp/hcl), [Dhall](https://github.com/dhall-lang/dhall-lang), [TOML](https://github.com/toml-lang/toml))

### XML
https://www.w3.org/XML/

 - (-) Not User friendly, too verbose
 - (-) Pure data format, no nice features
 - (+) Standard
 - (+) Very good schema validation form thanks to xsd

 ### JSON 
https://www.json.org/

 - (-) Not very human friendly (No comments, no multiline, ...)
 - (-) Not extensible, no formal way of defining custom types / processor 
 - (~) Json-Schema is still in draft but achieve the validation bit
 - (+) Decent eco-system (parsers, code generators, ...)

### YAML
http://www.yaml.org/start.html

 - (-) Slightly Complex
 - (-) No schema validation ([Kwalify](http://www.kuwata-lab.com/kwalify/) is Ruby only and very limited)
 - (+) JSON super-set
 - (+) Lots of human friendly features (Multiline, hash reference, multi documents, ...)
 - (+) Developer friendly with applications in mind
 - (+) Extensible through tags (Could potentially define a new type, a processor that handle env variable, etc...)

### HOCON
https://github.com/lightbend/config/blob/master/HOCON.md

 - (-) No schema validation
 - (-) Popular only in the scala world
 - (-) No extra feature compared to #YAML
 - (+) Simple and human friendly
 - (+) JSON super-set

## Goal

The Goal of this project is to provide some tools around one or several data formats to provide the following functionlities:
 - From a schema definition, generate serializer / deserialzer / validator code backed with strongly typed data structure
 - Reuse as much as possible the existing specs. Let's not invent yet another data format or schema language. If some feature are missing let's try to use their extension mechanism or worst case, be a super-set.
 - Be extensible :
    - Allow different data format as input/output
    - Allow the Definition of new type or new type mapping (e.g. mapping an array to a native LinkedList)
    - Allow some kind of custom processor (e.g. a processor that extracts from the environment)
    - Allow the generated code to be customizable (e.g. generate a toString method)
 - Be language agnostic to some extent. The concepts shouldn't be tide to the target language. Ideally this project could be ported to any language and reuse all the concepts.

 ### What's wrong with other tools

 Nothing, it just depends what you need. I believe Serde is one of the best designed library. The reasons why I won't use Serde, and other similar library in other languages are :
  - Serde has nothing else in mind than providing a library for (de)serializing to/from Rust and abstraact over the data language.
  - I want an engine that abstract over the data language, abstract over the schema and abstract over the programming language
  - I want to be able to plug custom behaviour into the parsing process (Resolving environment variable, pinging a provided url, ...)
  - I want this toolkit can be used for many different purpose:
    - Serializing / Deserializing
    - Generate code from schema and vice versa
    - Generating documentation 
    - Generating a compatible schema from sample data
    - Validating data from schema (with some options like validating incomplete document or document with data that will be resolved only at runtime, and provide a clear report of what can go wrong)
    - Formatting
    - ...
  - I want to be portable, use the existing standards as much as possible, generate parser code using the main libraries (Serde for Rust, Jackson for Java, ...)
  - Be embeddable as a library, a build plugin, ...


 ### Sub goals 
- Ideally we could validate HOCON document using a schema written in YAML and convert it to JSON 
- A tool to generate a schema from one or several sample data documents

 ## Scope

YAML seems the best input format due to its extensibilty. The concept of tags (both implicit and explicit) is very interesting and could be used for great good in our case. Once YAML is supported, other language seem trivial to support. Since YAML is a super of JSON, supporting JSON and JSON schema seems like a good first step. Initial implementation will be in Rust.

Steps :

 1. Create test data with to validate json against schema
 2. Create a parser / validator with extensibility in mind
 3. Support YAML, Extend Json-schema to have some 
 4. Create code generator
 5. Supports HOCON, TOML, ... 
 6. Port it to Scala, Haskell, Javascript, ...
 7. Conquer the world ¯\\(°_o)/¯