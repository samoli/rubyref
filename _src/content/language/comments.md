# Comments

Ruby has two types of comments: inline and block.

Inline comments are starting with `#` character and continue till the end of the line:

    # On a separate line
    class Foo # or at the end of the line
      # can be indented
      def bar
      end
    end

Block comments start with `=begin` and end with `=end`. Each should be on a separate line.

    =begin
    This is
    commented out
    =end
    class Foo
    end

`=begin` and `=end` can **not** be indented, so this is a syntax error:

    class Foo
      =begin
      Will not work
      =end
    end

## Comments as documentation

Ruby standard library comes with [RDoc](../developing/documenting.md) tool for generating documentation from code. RDoc format is "unobtrusive" (plain text) documentation. Ruby gem [YARD](https://yardoc.org/) is also frequently used for generate documentation, providing more strict format allowing to render "tags" (param types, return type, examples and so on).