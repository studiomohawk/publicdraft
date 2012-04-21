# Rsync Deploy config
backet = ""

# Tasks
desc "Given a title as an argument, create a new post file"
  task :write, [:title] do |t, args|
    filename = "#{Time.now.strftime('%Y-%m-%d')}-#{args.title.gsub(/\s/, '-').downcase}.markdown"
    path = File.join("_posts", filename)
    if File.exist? path; raise RuntimeError.new("Won't clobber #{path}"); end
    File.open(path, 'w') do |file|
      file.write <<-EOS
---
layout: post
title: "#{args.title}"
date: #{Time.now.strftime('%Y-%m-%d %k:%M:%S')}
category:
---
EOS
  end
  system "git add #{filename}"
  puts "Now open #{path} in an editor."
end

desc "Launch preview environment"
task :preview do
  system "jekyll --auto --server"
end

desc "Build LESS"
task :less-build do
  system "laessig observe style/style.less"
end

desc "Build site"
task :build do
  system "jekyll"
end

desc "Package app for production"
task :package do

  Rake::Task["build"].invoke

  system "git commit -am 'package is done'"
end

desc "Optimise all PNG files with optipng"
task :optipng do
  Dir.glob("_site/**/*.png").each do |file|
    system "optipng -quiet -o7 #{file}"
  end
end

desc "Deploy Amazon s3 Using s3Sync"
task :deploy do
  system('s3sync -rpv _site/ #{backet}:')
end