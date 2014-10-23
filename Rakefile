require 'yaml'
require 'erb'

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

def generate_novel_readme(novel, dst_path)
  out = <<-END
# #{novel['title']}

#{novel['description']}


<a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/"><img alt="Creative Commons License" style="border-width:0" src="https://i.creativecommons.org/l/by-nc/4.0/88x31.png" /></a><br /><span xmlns:dct="http://purl.org/dc/terms/" property="dct:title">#{novel['title']}</span> by <span xmlns:cc="http://creativecommons.org/ns#" property="cc:attributionName">Issei Naruta (mirakui)</span> is licensed under a <a rel="license" href="http://creativecommons.org/licenses/by-nc/4.0/">Creative Commons Attribution-NonCommercial 4.0 International License</a>.
  END
  File.open(dst_path, 'w') {|f| f.write out }
end

desc 'build all'
task build: %w[build:novels]

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

    desc 'build readmes'
    task :readme do
      meta['novels'].each do |novel|
        dir = novel['dir']
        path = "#{novel['dir']}/README.md"
        puts "generating #{path}"
        generate_novel_readme novel, path
      end
    end
  end
end
