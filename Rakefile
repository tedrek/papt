require 'date'
require 'digest'
require 'erb'
require 'time'

PKG = {
}

TEMPLATE = <<-HEREDOC
Format: 1.8
Date: <%= DateTime.now.rfc2822 %>
Source: papt
Binary: papt
Architecture: all
Version: <%= version %>
Distribution: xenial-personal
Urgency: medium
Maintainer: Tyler Hamilton <tyler@ablaze.ca>
Changed-By: Tyler Hamilton <tyler@ablaze.ca>
Description:
 papt       - Personal apt tool
Changes:
 papt (<%= version %>) unstable; urgency=medium
 .
   * See git
Checksums-Sha1:
 <%= Digest::SHA1.file(filename).hexdigest %> <%= File.size(filename) %> <%= filename %>
Checksums-Sha256:
 <%= Digest::SHA256.file(filename).hexdigest %> <%= File.size(filename) %> <%= filename %>
Files:
  <%= Digest::MD5.file(filename).hexdigest %> <%= File.size(filename) %> admin optional <%= filename %>
HEREDOC

def filename
  "papt_#{version}_all.deb"
end

def version
  commit_time = Time.parse(`git show -s --format=%ci HEAD`)
  return commit_time.utc.strftime('%Y%m%d%H%M')
end

task default: %w[build changes upload clean]

task :build do
  sh("fpm -s dir -t deb -n papt -v #{version} -a all " +
     "-m 'Tyler Hamilton <tyler@ablaze.ca>' " +
     "--vendor 'Tyler Hamilton <tyler@ablaze.ca>' " +
     "--url http://www.h.ablaze.ca/ " +
     "--description 'Personal apt utility' " +
     "--license gpl3 " +
     "--category admin " +
     "--deb-priority optional " +
     "papt=bin/papt pdpkg=bin/pdpkg")
end

task :changes do
  renderer = ERB.new(TEMPLATE)
  File.write("papt_#{version}_all.changes", renderer.result(binding))
end

task :upload do
  sh("scp papt_#{version}_all.deb papt_#{version}_all.changes " +
     "apt.serv.h.ablaze.ca:/srv/www/apt.serv.h.ablaze.ca/ubuntu/incoming")
end

task :clean do
  sh("rm papt_#{version}_all.deb papt_#{version}_all.changes")
end
