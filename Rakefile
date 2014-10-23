require 'pathname'
NOVEL_DIRECTORIES = %w[water elevator platinum]

def convert_txt_to_md(src_path, dst_path)
  dst_txt = File.read src_path
  dst_txt.gsub! /\n\n/m, '$P$'
  dst_txt.gsub! /\n/m, '$BR$'
  dst_txt.gsub! '$BR$', "  \n"
  dst_txt.gsub! '$P$', "\n\n"
  File.open(dst_path, 'w') {|f| f.write dst_txt }
end

desc 'build markdowns'
task :build do
  NOVEL_DIRECTORIES.each do |dir|
    sources = Dir.glob "#{dir}/src/*.txt"
    sources.each do |src|
      name = File.basename(src).split('.').first
      dst = "#{dir}/#{name}.md"
      puts "converting #{src} -> #{dst}"
      convert_txt_to_md src, dst
    end
  end
end
