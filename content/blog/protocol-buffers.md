+++
title = "Intro to Protocol Buffers"
date = "2024-10-20"
description = "A gentle introduction to serialization, JSON and Protocol Buffers"
tags = ["Programming"]
+++


To learn what Protocol buffers are, we need to know what Serialization means, the format that was and is still the most used, it's problems and how Protocol Buffers solves them and where to use Protocol Buffers.

## Serialization

All the data structures that we use are stored in memory which can only be accessed by the program that creates and uses it. Imagine that you have data stored in a data structure that you want to share with another program or service. How do you share the representation of data that you have in your memory? This is where serialization comes in. 

Serialization is the process of formatting and representing the data in a storable and transmittable way. No matter what the program is, it should be able to read the serialized data and deserialize it to get the original data consistently. One of the most popular formats, that we deal with a lot - CSV itself is a serialization format. Now that we know what serialization means, we can get into JSON, a common serialization format used in web and unstructured data applications.

## JSON

JSON stands for Javascript Object Notation. It is a widely used open standard serialization format . More than that, JSON is language independent despite its name, meaning no matter what programming language you use, you'll be able to read and write JSON objects consistently. If you know python, JSON is easier to understand, as it looks and functions exactly like a python dictionary. This is what an example JSON file looks like:


```
{
	'name': 'Harry Potter',
	'school': 'Hogwarts',
	'parents': ['James Potter', 'Lily Potter'],
	'no_of_friends': 2,
	'defeated_voldemort': True
}
```

As you can see, JSON is capable of handling different data types, integers, string, lists, booleans, etc., Everything in JSON is represented as key-value pairs. But JSON comes with it's own set of weaknesses as well. Image I'm adding the above data to the Hogwarts database. And a couple of moments later I'm adding the following entry

```
{
	'Name': 'Severus Snape',
	'school': 'Hogwarts',
	'class_taught': 'Defence agains the Dark Arts',
	'no_of_friends': 1,
	'defeated_voldemort': False
}
```

In this entry however, the name field has a different key (yes, capitalization of keys do make a difference), the parents field is missing and there is also a new field called class_taught. JSON allows this kind of non-rigid schemas, which can be a problem to some applications that requires certain columns. Another problem caused by this is the arbitrary allocation of memory when parsing a JSON file, since it does not have any pre-defined data types. The presence of keys also causes the JSON files to be bigger in size. 

## Enter Protocol buffers

>Protocol Buffers are a language-neutral, platform-neutral extensible mechanism for serializing structured data.

The above definition is directly quoted from the webpage of protocol buffers. Protocol buffers allows us to create a rigid schema for the data that we are expecting to store/transmit. We can do this by creating a `.proto` file. Then this file can be compiled to any language using a proto compiler called `protoc`. The resultant code can be used in conjunction with the program that we are using. They solve a lot of problems that JSON has for structured data transmission. 
1. It enforces a rigid schema, the output serialized data is of predictable size and allows faster parsing, and they also offer the flexibility to update the schema in future without affecting the functionality. 
2. It has a smaller disk and network footprint.

A sample `.proto` file for our Hogwarts use case would look like:

```protobuf
message Wizard {
	required string name = 1;
	required string school = 2;
	required int32 no_of_friends = 3;
	required bool defeated_voldemort = 4;
}
```

The above sample is a perfect example of rigid schema enforced by Protocol Buffer. It also allows adding additional optional parameters by using the `optional` keyword in front instead of `required`. The numbers at the end of each field are used instead of field names during serialization to save space. This is because numbers are more compact than field names when encoding the data. Numbers from `1` to `15` take **1 byte** in the encoded format, while numbers `16` to `2047` take **2 bytes**. Therefore, it's good practice to use field numbers between `1` and `15` for fields that are used frequently or are performance-critical.

Now to compile this, we can use `protoc` the protobuf compiler.

```bash
protoc --proto_path=$SRC_DIR --python_out=$DST_DIR $SRC_DIR/wizard.proto
```

In the real world however, we'll also be defining `--grpc_python_out`  which is associated with defining gRPC services using protobufs, which we will not covering in this article. However it is not mandatory to define that parameter.

The file generated by the protoc compiler will have classes which we can use to create objects that follow the schema that we defined in the `.proto` file.

## Where to use JSON and Protocol Buffers

| Feature          | JSON                            | Protocol Buffers                     |
| ---------------- | ------------------------------- | ------------------------------------ |
| Schema           | Flexible                        | Rigid, defined via `.proto` file     |
| Readability      | Human-readable                  | Binary format (not human-readable)   |
| Parsing Speed    | Slower                          | Faster                               |
| File Size        | Larger due to text-based format | Smaller, compact binary encoding     |
| Language Support | Universal                       | Requires generated code (via protoc) |

Use JSON
1. When you specifically want a flexible schema
2. Readable data
3. Less volume of data

Use Protocol Buffers
1. When you want rigid schema
2. Faster serialization
3. Efficient storage

## An Ending note

This entire article is created to help you understand what Protocol Buffers is and give you a basic intro to it. There are a lot of topics which I have not covered here, topics that I myself have not learnt and the use cases of these formats - REST and gRPC. I also have a speed comparison of REST and gRPC implementation which uses JSON and Protobufs respectively, which you can find [here](https://github.com/dhanavikram/REST-v-gRPC). Now that you have a basic understanding, I highly recommend you to read the [documentation](https://protobuf.dev/) of Protocol Buffers. Where else to learn if not from the creator? 