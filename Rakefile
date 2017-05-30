# for GitHub Pages
DIST_DIR = "docs"

desc "generate slide to #{DIST_DIR}/"
task :generate do
  sh "reveal-ck generate --dir=#{DIST_DIR}"
end

desc "serve slide"
task :serve => :generate do
  sh "reveal-ck serve --dir=#{DIST_DIR}"
end

task default: :serve
