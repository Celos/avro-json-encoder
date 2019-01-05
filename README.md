# avro-json-encoder
JSON encoder for AVRO that skips default values and encodes simple null unions in a more sane fashion. Based on org.apache.avro.io.JsonEncoder from <a href="https://github.com/apache/avro">AVRO</a> 1.8.2 and org.apache.avro.io.ExtendedJsonEncoder by <a href="https://github.com/zolyfarkas/avro">zolyfarkas</a>.

**This encoder is meant to be used with <a href="https://github.com/Celos/avro-json-decoder">avro-json-decoder</a> as it is capable of decoding Avro JSON with missing values.**

## why

Given this schema (in AVRO IDL)

```
record User {
  string username;
  union {null, string} name = null;
}
```
a user without a name will be encoded as
```json
{"username":"user1","name":null}
```
and a user with a name will be encoded as
```json
{"username":"user1","name":{"string":"Jake Peralta"}}
```
Using this encoder, the output will instead be
```json
[
  {"username":"user1"},
  {"username":"user1","name":"Jake Peralta"}
]
```
The encoder allows skipping specifying nulls. Use with <a href="https://github.com/Celos/avro-json-decoder">avro-json-decoder</a>.

## how

Replace

```java
JsonEncoder encoder = EncoderFactory.get().jsonEncoder(SCHEMA, OUTPUT_STREAM);
```
with
```java
ExtendedJsonEncoder encoder = new ExtendedJsonEncoder(SCHEMA, OUTPUT_STREAM);
```
and pass it to the new ExtendedGenericDatumWriter:
```java
DatumWriter<T> writer = new ExtendedGenericDatumWriter<>(SCHEMA_OR_CLASS);
writer.write(DATA, encoder);
```
