
# raabro

[![Build Status](https://secure.travis-ci.org/jmettraux/raabro.png)](http://travis-ci.org/jmettraux/raabro)
[![Gem Version](https://badge.fury.io/rb/raabro.png)](http://badge.fury.io/rb/raabro)

A very dumb PEG parser library.

Son to [aabro](https://github.com/flon-io/aabro), grandson to [neg](https://github.com/jmettraux/neg), grand-grandson to [parslet](https://github.com/kschiess/parslet).

## a sample parser/rewriter

```ruby
require 'raabro'


module Fun include Raabro

  # parse
  #
  # Last function is the root, "i" stands for "input".

  def pa(i); rex(nil, i, /\(\s*/); end
  def pz(i); rex(nil, i, /\)\s*/); end
  def com(i); rex(nil, i, /,\s*/); end

  def num(i); rex(:num, i, /-?[0-9]+\s*/); end

  def args(i); eseq(:args, i, :pa, :exp, :com, :pz); end
  def funame(i); rex(:funame, i, /[a-z][a-z0-9]*/); end
  def fun(i); seq(:fun, i, :funame, :args); end

  def exp(i); alt(:exp, i, :fun, :num); end

  # rewrite
  #
  # Names above (:num, :fun, ...) get a rewrite_xxx function.
  # "t" stands for "tree".

  def rewrite_exp(t); rewrite(t.children[0]); end
  def rewrite_num(t); t.string.to_i; end

  def rewrite_fun(t)
    [ t.children[0].string ] +
    t.children[1].children.inject([]) { |a, e| a << rewrite(e) if e.name; a }
  end
end


p Fun.parse('mul(1, 2)')
  # => ["mul", 1, 2]

p Fun.parse('mul(1, add(-2, 3))')
  # => ["mul", 1, ["add", -2, 3]]

p Fun.parse('mul (1, 2)')
  # => nil (doesn't accept a space after the function name)
```

This sample is available at: [doc/readme0.rb](doc/readme0.rb).


## LICENSE

MIT, see [LICENSE.txt](LICENSE.txt)

