# enum-type

A Java inspired, type safe Enum implementation in Ruby.

## Installation

Install via Ruby gems. This gem is not yet published to Ruby gems, so for now, install by setting the git source directly.

In your Gemfile, add:

```
gem 'enum-type', git: 'https://github.com/thegorgon/enum-type.git'
```


## Usage

enum-type allows you to define simple enums, enums with custom attributes and enums with type safe custom attributes.

### Simple Enum

A simple enum can be defined with `EnumType.create`:

```
COLORS = EnumType.create do
  RED(:red)
  GREEN(:green)
  BLUE(:blue)
end
```

Enum names must be capitalized and enums must be defined with at least one argument. Any number of enumerations can be defined and any value can be used as the value.

Enumerations will then be available as uppercase members of the returned enum type. Enumerations always have `name` and `value` attributes, as in:

```
COLORS.RED         # => #<struct Struct::Enum name='RED' value=:red>
COLORS.RED.name    # => 'RED'
COLORS.RED.value   # => :red
```

### Enum with Custom Attributes

As in Java, sometimes you may want to define an enum where each enumeration has custom attributes. This can be done by passing a list of attribute names to `EnumType.create`.

```
COLORS = EnumType.create(:hex, :rgb) do
  RED(:red, '#f00', [255, 0, 0])
  GREEN(:green, '#0f0', [0, 255, 0])
  BLUE(:blue, '#00f', [0, 0, 255])
end
```

This still defines `name` and `value` on each enumeration, but also defines custom properties, `hex` and `rgb`, as in:

```
COLORS.RED         # => #<struct Struct::Enum name='RED' value=:red>
COLORS.RED.name    # => 'RED'
COLORS.RED.value   # => :red
COLORS.RED.hex     # => '#f00'
COLORS.RED.rgb     # => [255, 0, 0]
```

### Enum with Typesafe Custom Attributes

You can also add type safety to your custom attributes by passing `EnumType.create` a Hash of names and types. Type checking is achieved by calling `Object#is_a?`, so any object that passes `object.is_a?(YourType)` will match `YourType`.

Without type safety, the value of the enum can be any object, so when adding type safety, you must also define the type of your value by including a `value` key in the Hash argument to `EnumType.create`.

```
COLORS = EnumType.create(value: Symbol, hex: String, rgb: Array) do
  RED(:red, '#f00', [255, 0, 0])
  GREEN(:green, '#0f0', [0, 255, 0])
  BLUE(:blue, '#00f', [0, 0, 255])
end
```

This behaves the same way as the array of property names, but checks the type of all enumeration properties. For example, this will raise an error:

```
COLORS = EnumType.create(value: Symbol, hex: String, rgb: Array) do
  RED(:red, '#f00', [255, 0, 0])
  GREEN(:green, 27, [0, 255, 0])
  BLUE('blue', '#00f', 'rgb(0,0,255)')
end
```

### Using Enums

Enums are enumerable, so you can use any method from the [Enumerable](https://ruby-doc.org/core-2.4.1/Enumerable.html) module:

```
COLORS.each { |enum| puts enum.name } # => prints RED, GREEN and BLUE.
COLORS.pluck(:value) # => [:red, :green, :blue]
```

To lookup an enum by name (as a string or symbol) or value, simply use the array access method, `[]`:

```
# By value
COLORS[:red] === COLORS.RED # => true
# By name as a symbol
COLORS[:RED] === COLORS.RED # => true
# By name as a string
COLORS['RED'] === COLORS.RED # => true
```

You can also access all the enums using `#entries` or just get a list of names (as symbols) or values using `#values` or `#names`.

```
COLORS.entries # => [COLORS.RED, COLORS.GREEN, COLORS.BLUE]
COLORS.values # => [:red, :green, :blue], equivalent to #pluck(:value)
COLORS.names # => [:RED, :GREEN, :BLUE], equivalent to #pluck(:name)

## Contributing

Pull requests welcome!
