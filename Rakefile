require 'yaml'

def meta
  @meta ||= YAML.load(File.read('metadata.yml'))
end

def convert_txt_to_md(src_path, dst_path)
  dst_txt = File.read src_path
  dst_txt.gsub! /\n\n/m, '$P$'
  dst_txt.gsub! /\n/m, '$BR$'
  dst_txt.gsub! '$BR$', "  \n"
  dst_txt.gsub! '$P$', "\n\n"
  File.open(dst_path, 'w') {|f| f.write dst_txt }
end

def novel_license(novel=nil)
  if novel
    return %Q!<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">#{novel['title']}</span> by <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Issei Naruta (mirakui)</span> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.!
  else
    return %Q!<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">All novels in this repository by <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Issei Naruta (mirakui)</span> are licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.!
  end
end

def char_count(novel)
  path = "#{novel['dir']}/#{novel['dir']}.md"
  txt = File.read(path)
  txt.gsub(/[　\s#]/, '').length
end

def generate_novel_readme(novel, dst_path)
  out = <<-END
# #{novel['title']}

#{novel['description']}

#{novel_license(novel)}
  END
  File.open(dst_path, 'w') {|f| f.write out }
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

  meta['novels'].each do |novel|
    url = "https://github.com/mirakui/mirakui-novels/tree/master/#{novel['dir']}"
    out += <<-END
| [#{novel['title']}](#{url}) | #{char_count(novel)} 字 | #{novel['description']} |
    END
  end

  out += <<-FOOTER

---

## License
Copyright (c) 2014 Issei Naruta.

### Novels
#{novel_license}

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
      meta['novels'].each do |novel|
        dir = novel['dir']
        sources = Dir.glob "#{dir}/src/*.txt"
        sources.each do |src|
          name = File.basename(src).split('.').first
          dst = "#{dir}/#{name}.md"
          puts "converting #{src} -> #{dst}"
          convert_txt_to_md src, dst
        end
      end
    end

    desc 'build README.md for each novels'
    task :readme do
      meta['novels'].each do |novel|
        dir = novel['dir']
        path = "#{novel['dir']}/README.md"
        puts "generating #{path}"
        generate_novel_readme novel, path
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
