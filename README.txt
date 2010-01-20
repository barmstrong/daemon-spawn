A slight motification of the excellent daemon-spawn gem that makes it easier to run multiple workers!

Original Gem:  http://github.com/alexvollmer/daemon-spawn


== EXAMPLE OF RUNNING MULTIPLE WORKERS:


#!/opt/ruby/bin/ruby
require 'rubygems'
#my fork of daemon-spawn!
require 'daemon-spawn'

RAILS_ROOT = File.expand_path(File.join(File.dirname(__FILE__), '..'))

class ResqueWorker < DaemonSpawn::Base
  def start(args)
    ENV['RAILS_ENV'] ||= args.first || 'development'
    Dir.chdir RAILS_ROOT
    require File.join('config', 'environment')

    Resque::Worker.new("*").work
  end

  def stop
    system("kill `cat #{self.pid_file}`")
  end
end

# Change number here to increase/decrease number of workers
num_workers = 4

num_workers.times do |i|
  ResqueWorker.spawn!(:log_file => File.join(RAILS_ROOT, "log", "resque_worker_#{i}.log"),
    :pid_file => File.join(RAILS_ROOT, 'tmp', 'pids', "resque_worker_#{i}.pid"),
    :sync_log => true,
    :working_dir => RAILS_ROOT)
end



== INSTALL:

* sudo gem install barmstrong-daemon-spawn

== LICENSE:

(The MIT License)

Copyright (c) 2009 Evri, Inc.

Permission is hereby granted, free of charge, to any person obtaining
a copy of this software and associated documentation files (the
'Software'), to deal in the Software without restriction, including
without limitation the rights to use, copy, modify, merge, publish,
distribute, sublicense, and/or sell copies of the Software, and to
permit persons to whom the Software is furnished to do so, subject to
the following conditions:

The above copyright notice and this permission notice shall be
included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED 'AS IS', WITHOUT WARRANTY OF ANY KIND,
EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF
MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT.
IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY
CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT,
TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE
SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.
