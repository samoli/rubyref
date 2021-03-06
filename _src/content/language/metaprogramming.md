# Metaprogramming and Domain-Specific Languages

This chapter briefly investigates two advanced concepts which Ruby provides: metaprogramming and building domain-specific languages which are also valid Ruby code.

## Metaprogramming

Ruby is known to have very powerful **metaprogramming** capabilities, that is, defining language
structures (classes, modules, methods) at runtime. Unlike many other languages, Ruby's
metaprogramming does not use special constructs different from "normal" programming, like macros,
decorators or templates. The power of Ruby's metaprogramming comes from two facts:

* Everything is an object;
* Core classes are hackable.

### Everything is an object

That also include core language concepts like classes and methods. They can be inspected and changed
at runtime.

    class A
      def m(x, y)
      end
    end

    A.class   #=> Class
    A.methods #=> [:new, :allocate, :superclass, ....
    A.instance_method(:m) #=> #<UnboundMethod: A#m>
    A.instance_method(:m).parameters #=> [[:req, :x], [:req, :y]]

### Core classes are hackable

Core classes (like `Class`, `Module`, `Method`) provide methods to change their behavior and define
new concepts with regular Ruby code:

    # Define methods with names stored in variable
    class A
      [:+, :-, :*, :/].each do |op|
        define_method(op) {
          # ...
        }
      end
    end

    # Call methods, names stored in variable
    [:+, :-, :*, :/].map { |op| 100.send(op, 10) } #=> [110, 90, 1000, 10]

    # Create classes and assign to constants
    [:A, :B, :C].each { |name| Kernel.const_set(name, Class.new) }

### Example of the metaprogramming

It is a common pattern to have some variable calculated on first method call:


    def value
      @value ||= expensive_calculation
    end

With metaprogramming, we can encapsulate this pattern into `memoize` method, allowing to write code like this:

    memoize def value
      expensive_calculation
    end

The implementation can look like this:

    def self.memoize(method_name) # define class-level method receiving method name to memoize
      original = instance_method(name) # storing previous implementation in variable, it is UnboundMethod
      ivar_name = :"@#{name}"

      define_method(name) do  # redefine method with name provided
        # return variable if it is set
        return instance_variable_get(ivar_name) if instance_variables.include?(ivar_name)
        # or calculate variable with previous method implementation and store it
        instance_variable_set(ivar_name, original.bind(self).call)
      end
    end

> **Note**: This implementation is naive and just shows the principle. See third-party libraries like [memoist](https://github.com/matthewrudy/memoist) for a proper implementation.

See [Language Core](../builtin/core.md) classes documentation to understand what you can do with
core objects.

## Domain-Specific Languages

Ruby is naturally flexible enough for defining clean and readable sublanguages by means of _methods_ and _blocks_. Short example (taken from the syntax of the [RSpec](http://rspec.info/) testing library):

    RSpec.describe Calculator do
      subject { Calculator.add(number1, number2) }
      let(:number1) { 5 }
      let(:number2) { 6 }

      it 'adds numbers' do
        expect(subject).to eq 11
      end
    end

All components this example built with are just methods, their arguments and blocks:

    RSpec.describe Calculator do                          # Method RSpec.describe() with argument Calculator and
                                                          # block of code (which will be later evaluated)
      subject { Calculator.add(number1, number2) }        # Method subject() with block of code defining test subject
      let(:number1) { 5 }                                 # Method let() with argument :number1 and block of code
      let(:number2) { 6 }

      it 'adds numbers' do                                # Method it() with argument 'adds numbers' and block of code
                                                          # defining what to test
        expect(subject).to eq 11
      end
    end

Note how varying of elements of syntax (optional paretheses around method arguments and `{}` vs `do / end` around blocks) allow to create boilerplate-less definition of tests.