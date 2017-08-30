---
layout: post
title: "thor_for_rails_scaffold_generate_path"
date: 2017-08-30 12:15:08 +0900
comments: true
categories: [ruby]
tags: [thor, rails, generator, jbuilder]
---

## TL;DR

If you want to override jbuilder generator when scaffolding, put it `Rails.root + lib/templates/rails/jbuilder/index.json.jbuilder`

`vim /Users/vimtaku/.rbenv/versions/2.2.3/lib/ruby/gems/2.2.0/gems/thor-0.19.1/lib/thor/actions.rb`

```
def find_in_source_paths(file) # rubocop:disable MethodLength
  possible_files = [file, file + TEMPLATE_EXTNAME]
  relative_root = relative_to_original_destination_root(destination_root, false)
  binding.pry

  source_paths.each do |source|
    possible_files.each do |f|
      source_file = File.expand_path(f, File.join(source, relative_root))
      return source_file if File.exist?(source_file)
    end
  end
```

## Background
I searched pathes for used generator, but I cant find where template is used.
I'm using jbuilder.

## lib/templates
`Rails.root + lib/templates` dir is used as tempaltes path by default.  
if you want to override jbuilder templates, you can put  
`Rails.root + lib/templates/rails/jbuilder/index.json.jbuilder`.

## see also
`thor-0.19.1/lib/thor/actions/file_manipulation.rb`
