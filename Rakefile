file "data.json" do
  system "curl -o data.json http://coub.com/api/v2/timeline/channel/oftheday?per_page=900"
end

file "media_only.json" => "data.json" do
  system "jq -c '[.coubs[] | { video: .file_versions.iphone.url, audio: .file_versions.mobile.audio_url, has_sound: .has_sound }]' data.json >media_only.json"
end

task :upload => "media_only.json" do
  system 's3cmd --bucket-location=EU sync --rexclude="^\..*|data.json|Rakefile" --acl-public . "s3://coubloop.tv" --verbose'
end

task :serve => "media_only.json" do
  puts "Starting WEBrick"
  exec "ruby -rwebrick -e'WEBrick::HTTPServer.new(:Port => 3000, :DocumentRoot => Dir.pwd).start'"
end

task :default => [:serve]
