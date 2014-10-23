require 'yaml'
require 'pathname'

def meta
  @meta ||= YAML.load(File.read('metadata.yml'))
end

class Novel
  attr_reader :title, :description

  def initialize(hash)
    @dir = hash['dir']
    @title = hash['title']
    @description = hash['description']
  end

  def base_path
    Pathname(@dir)
  end

  def readme_path
    base_path.join('README.md')
  end

  def md_path
    base_path.join("#{@dir}.md")
  end

  def github_url
    "https://github.com/mirakui/mirakui-novels/tree/master/#{@dir}/#{@dir}.md"
  end

  def source_paths
    Pathname.glob(base_path.join('src/**.txt'))
  end

  def generate_md
    dst_md = ''
    source_paths.each do |src_path|
      src_txt = src_path.read
      dst_md += convert_txt_to_md(src_txt)
    end
    md_path.open('w') {|f| f.write dst_md }
  end

  def generate_readme
    out = <<-END
# #{title}

#{description}

[この作品を読む](#{github_url})

---

#{license}
  END
    readme_path.open('w') {|f| f.write out }
  end

  def char_count
    txt = md_path.read
    txt.gsub(/[　\s#]/, '').length
  end

  def license
    %Q!<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">#{title}</span> by <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Issei Naruta (mirakui)</span> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.!
  end

  private

  def convert_txt_to_md(txt)
    txt = txt.dup
    txt.gsub! /\n\n/m, '$P$'
    txt.gsub! /\n/m, '$BR$'
    txt.gsub! '$BR$', "  \n"
    txt.gsub! '$P$', "\n\n"
    txt
  end
end

def generate_index_readme(dst_path)
  out = <<-HEADER
# mirakui-novels

Novels by mirakui

  HEADER

  out += <<-TABLE_HEADER
| title | size | first published |
| ----- | ---- | --------------- |
  TABLE_HEADER

  meta['novels'].each do |_novel|
    novel = Novel.new(_novel)
    out += <<-END
| [#{novel.title}](#{novel.github_url}) | #{novel.char_count} 字 | #{novel.description} |
    END
  end

  out += <<-FOOTER

---

## License
Copyright (c) 2014 Issei Naruta.

### Novels
<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">All novels in this repository by <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Issei Naruta (mirakui)</span> are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.

### Other files
Files except novels are licensed under the [MIT License](http://opensource.org/licenses/MIT).
  FOOTER
  File.open(dst_path, 'w') {|f| f.write out.to_s }
end

desc 'build all'
task build: %w[build:novels build:index_readme]

namespace :build do
  task novels: %w[novels:markdown novels:readme]

  namespace :novels do
    desc 'build markdowns'
    task :markdown do
      meta['novels'].each do |_novel|
        novel = Novel.new(_novel)
        puts "generating #{novel.md_path}"
        novel.generate_md
      end
    end

    desc 'build README.md for each novels'
    task :readme do
      meta['novels'].each do |_novel|
        novel = Novel.new(_novel)
        puts "generating #{novel.readme_path}"
        novel.generate_readme
      end
    end
  end

  desc 'build index README.md'
  task :index_readme do
    path = 'README.md'
    puts "generating #{path}"
    generate_index_readme path
  end
end
