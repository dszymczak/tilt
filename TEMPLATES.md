Tilt Templates
==============

While all Tilt templates use the same basic interface for template loading and
evaluation, each varies in its capabilities and available options. Detailed
documentation on each supported template engine is provided below.

 * [ERB](#erb)
 * [Erubis](#erubis)
 * [Haml](#haml)
 * [Mustache](#mustache)


<a name='erb'></a>
ERB (`erb`, `rhtml`)
--------------------

An easy to use but powerful templating system for Ruby.

### Example

    Hello <%= world %>!

### Usage

The `Tilt::ERBTemplate` class is registered for all files ending in `.erb` or
`.rhtml` by default. ERB templates support custom evaluation scopes and locals:

    >> require 'erb'
    >> template = Tilt.new('hello.html.erb', :trim => '<>')
    => #<Tilt::ERBTemplate @file='hello.html.erb'>
    >> template.render(self, :world => 'World!')
    => "Hello World!"

Or, use the `Tilt::ERBTemplate` class directly to process strings:

    require 'erb'
    template = Tilt::ERBTemplate.new(nil, :trim => '<>') { "Hello <%= world %>!" }
    template.render(self, :world => 'World!')

__NOTE:__ It's suggested that your program `require 'erb'` at load time when
using this template engine within a threaded environment.

### Options

#### `:trim => '-'`

The ERB trim mode flags. This is a string consisting
of any combination of the following characters:

  * `'>'`  omits newlines for lines ending in `>`
  * `'<>'` omits newlines for lines starting with `<%` and ending in `%>`
  * `'-'`  omits newlines for lines ending in `-%>`.
  * `'%'`  enables processing of lines beginning with `%`

#### `:safe => nil`

The `$SAFE` level; when set, ERB code will be run in a
separate thread with `$SAFE` set to the provided level.

### See also

  * [ERB documentation](http://www.ruby-doc.org/stdlib/libdoc/erb/rdoc/classes/ERB.html)


<a name='erubis'></a>
Erubis (`erubis`)
-----------------

Erubis is a fast, secure, and very extensible implementation of eRuby.

### Usage

To use Erubis instead of ERB for all `.erb` and `.rhtml` files, register
the extensions as follows:

    Tilt.register 'erb', Tilt::ErubisTemplate
    Tilt.register 'rhtml', Tilt::ErubisTemplate

### Options

#### `:trim => true` 

Delete spaces around '<% %>'. (But, spaces around '<%= %>' are preserved.)

#### `:pattern => '<% %>'`

Set pattern for embedded Ruby code.

See the [ERB](#erb) template documentation for examples, usage, and options.

__NOTE:__ It's suggested that your program `require 'erubis'` at load time when
using this template engine within a threaded environment.

### See also

  * [Erubis Home](http://www.kuwata-lab.com/erubis/)
  * [Erubis User's Guide](http://www.kuwata-lab.com/erubis/users-guide.html)


<a name='haml'></a>
Haml (`haml`)
-------------

Haml is a markup language that’s used to cleanly and simply describe the HTML of
any web document without the use of inline code. Haml functions as a replacement
for inline page templating systems such as PHP, ASP, and ERB, the templating
language used in most Ruby on Rails applications. However, Haml avoids the
need for explicitly coding HTML into the template, because it itself is a
description of the HTML, with some code to generate dynamic content.
([more](http://haml-lang.com/about.html))


### Example

    %html
      %head
        %title= @title
      %body
        %h1
          Hello
          = world + '!'

### Usage

The `Tilt::HamlTemplate` class is registered for all files ending in `.haml`
by default. Haml templates support custom evaluation scopes and locals:

    >> require 'haml'
    >> template = Tilt.new('hello.haml')
    => #<Tilt::HamlTemplate @file='hello.haml'>
    >> @title = "Hello Haml!"
    >> template.render(self, :world => 'Haml!')
    => "
    <html>
      <head>
        <title>Hello Haml!</title>
      </head>
      <body>
        <h1>Hello Haml!</h1>
      </body>
    </html>"

Or, use the `Tilt::HamlTemplate` class directly to process strings:

    >> require 'haml'
    >> template = Tilt::HamlTemplate.new { "%h1= 'Hello Haml!'" }
    => #<Tilt::HamlTemplate @file=nil ...>
    >> template.render
    => "<h1>Hello Haml!</h1>"

__NOTE:__ It's suggested that your program `require 'haml'` at load time when
using this template engine within a threaded environment.

### Options

#### `:format => :xhtml`

Determines the output format. The default is `:xhtml`. Other options are
`:html4` and `:html5`, which are identical to `:xhtml` except there are no
self-closing tags, the XML prolog is ignored and correct DOCTYPEs are generated.

#### `:escape_html => false`

Sets whether or not to escape HTML-sensitive characters in script. If this is
true, `=` behaves like `&=;` otherwise, it behaves like `!=`. Note that if this
is set, `!=` should be used for yielding to subtemplates and rendering partials.
Defaults to false.

#### `:ugly => false`

If set to true, Haml makes no attempt to properly indent or format the HTML
output. This causes the rendering to be done much quicker than it would
otherwise, but makes viewing the source unpleasant. Defaults to false.

#### `:suppress_eval => false`

Whether or not attribute hashes and Ruby scripts designated by `=` or `~` should
be evaluated. If this is true, said scripts are rendered as empty strings.
Defaults to false.

#### `:attr_wrapper => "'"`

The character that should wrap element attributes. This defaults to `'` (an
apostrophe). Characters of this type within the attributes will be escaped (e.g.
by replacing them with `&apos;`) if the character is an apostrophe or a
quotation mark.

#### `:autoclose => %w[meta img link br hr input area param col base]`

A list of tag names that should be automatically self-closed if they have no
content. Defaults to `['meta', 'img', 'link', 'br', 'hr', 'input', 'area',
'param', 'col', 'base']`.

#### `:preserve => %w[textarea pre]`

A list of tag names that should automatically have their newlines preserved
using the `Haml::Helpers#preserve` helper. This means that any content given on
the same line as the tag will be preserved. For example, `%textarea= "Foo\nBar"`
compiles to `<textarea>Foo&#x000A;Bar</textarea>`. Defaults to `['textarea',
'pre']`.

#### `:encoding => 'utf-8'`

The encoding to use for the HTML output. Only available in Ruby 1.9 or higher.
This can be a string or an Encoding Object. Note that Haml does not
automatically re-encode Ruby values; any strings coming from outside the
application should be converted before being passed into the Haml template.
Defaults to `Encoding.default_internal` or, if that's not set, `"utf-8"`.

### See also

  * [#haml.docs](http://haml-lang.com/docs.html)
  * [Haml Tutorial](http://haml-lang.com/tutorial.html)
  * [Haml Reference](http://haml-lang.com/docs/yardoc/HAML_REFERENCE.md.html)
  * [Whitespace Preservation](http://haml-lang.com/docs/yardoc/HAML_REFERENCE.md.html#whitespace_preservation)


<a name='mustache'></a>
Mustache (`mustache`)
---------------------

Mustache is a framework-agnostic way to render logic-free views.

### Options

__NOTE:__ It's suggested that your program `require 'mustache'` at load time
when using this template engine in a threaded environment.

#### `:path => Dir.pwd`

The base path where mustache templates (`.html` files) are located.  Defaults to
the current working directory.

#### `:template_extension => 'html'`

The file extension used on mustache templates. Default is `'html'`.

#### `:namespace => Object`

The class or module where View classes are located. If you have
`Hurl::App::Views`, namespace should be `Hurl:App`. This defaults to `Object`,
causing `::Views` to be searched for classes.

#### `:mustaches => nil`

Where mustache views (.rb files) are located, or nil to disable auto-requiring
of views based on template names. By default, the view file is assumed to be in
the same directory as the template file.

All other options are assumed to be attribute writer's on the Mustache
class and are set when a template is compiled. They are:

#### `:view => nil`

The Mustache subclass that should be used a the view. When this option is
specified, the template file will be determined from the view class, and the
`:namespace` and `:mustaches` options are irrelevant.

### See also

  * [Mustache Docs](http://defunkt.github.com/mustache/)
  * [defunkt/mustache](http://github.com/defunkt/mustache) on GitHub
