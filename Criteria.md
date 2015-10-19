Criteria are assertions about element properties, that can be true or false for an element. These are part of the client-RC protocol for actions such as 'find descendants'.

There is a corresponding Criteria type in the client library.

# Example #
"It is a button and its name is Toby" has the following JSON representation:
```
{
  "type":"and",
  "target": [
    {
      "type":"property",
      "name":"controlType",
      "value":"Button"
    },
    {
      "type":"property",
      "name":"name",
      "value":"Toby"
    }
  ]
}
```

To create this Criteria in Java:
```
Criteria c = Criteria.type(Button.class).and(Criteria.name("Toby"));
Criteria c = type(Button.class).and(name("Toby")) // with a static import
```