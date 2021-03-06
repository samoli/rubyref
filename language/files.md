---
title: File Structure
prev: "/language/misc.html"
next: "/language/globals.html"
---

## File Structure of Ruby Program

Ruby does not enforces any particular file structure. Loading of code
from different files is performed by <a
href='https://ruby-doc.org/core-2.5.0/Kernel.html#method-i-require'
class='ruby-doc remote' target='_blank'>require</a>, <a
href='https://ruby-doc.org/core-2.5.0/Kernel.html#method-i-require_relative'
class='ruby-doc remote' target='_blank'>require\_relative</a> or <a
href='https://ruby-doc.org/core-2.5.0/Kernel.html#method-i-require_relative'
class='ruby-doc remote' target='_blank'>load</a>. Filesystem structure
is not related to modules structure, because all constants (including
classes and modules) from loaded files are imported into the global
namespace.

Where Ruby looks for the `require`-d files is controlled by `$LOAD_PATH`
constant. If the file not found,
[LoadError](../builtin/exception/exception-classes.md#loaderror)
exception is raised and can be caught. This allows implementation of
"optional library" pattern:


```ruby
begin
  require 'somelibrary'
rescue LoadError
  warn "You need to install somelibrary to access <some functionality>"
end
```

There are soft community conventions, suggesting one class or module per
file and correspondence between filesystem and module structure (e.g.
`require 'library_name/namespace/file'` should load
`LibraryName::Namespace::Module`), though it is neither required nor
enforced by default.

See also:

* [Standard Library](../stdlib.md)
* [Third-party Libraries](../developing/libraries.md)
* [Code Style and Linting](../developing/code-style.md)

