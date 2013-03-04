RIAK_VERSION      = "1.3.0"
RIAK_DOWNLOAD_URL = "http://s3.amazonaws.com/downloads.basho.com/riak/#{RIAK_VERSION[0..2]}/#{RIAK_VERSION}/riak-#{RIAK_VERSION}.tar.gz"
NUM_NODES = 5

task :default => :help

task :help do
  sh %{rake -T}
end

desc "install, start and join riak nodes"
task :bootstrap => [:install, :start, :join]

desc "start all riak nodes"
task :start => [:start_nodes, :report_bindings]

task :start_nodes do
  (1..NUM_NODES).each do |n|
    sh %{ulimit -n 4096; ./riak#{n}/bin/riak start}
  end
  puts "======================================="
  puts "Riak Dev Cluster started"
  puts "======================================="
end

task :report_bindings do
  pb_ip      = `perl -n -e 'print $1 if /.*pb_ip.*"(\\d+.\\d+.\\d+.\\d+)".*/' riak1/etc/app.config`
  pb_port    = `perl -n -e 'print $1 if /.*pb_port[^\\d]*(\\d+).*/' riak1/etc/app.config`
  http_ip    = `perl -n -e 'print $1 if /.*http[^\\w].*"(\\d+.\\d+.\\d+.\\d+)".*/' riak1/etc/app.config`
  http_port  = `perl -n -e 'print $1 if /.*http[^\\w].*"\\d+.\\d+.\\d+.\\d+"[^\\d]*(\\d+).*/' riak1/etc/app.config`
  https_ip   = `perl -n -e 'print $1 if /.*https[^\\w].*"(\\d+.\\d+.\\d+.\\d+)".*/' riak1/etc/app.config`
  https_port = `perl -n -e 'print $1 if /.*https[^\\w].*"\\d+.\\d+.\\d+.\\d+"[^\\d]*(\\d+).*/' riak1/etc/app.config`
  eth0_ip    = `ifconfig  | grep 'inet addr:'| grep -v '127.0.0.1' | head -1 | cut -d: -f2 | awk '{ print $1 }'`.chomp!
  pb_ip = eth0_ip if pb_ip == "0.0.0.0"
  http_ip = eth0_ip if http_ip == "0.0.0.0"
  https_ip = eth0_ip if https_ip == "0.0.0.0"
  puts "======================================="
  puts "HTTP API: http://#{pb_ip}:#{pb_port}"
  puts
  puts "Admin UI: http://#{http_ip}:#{http_port}/admin"
  puts "          https://#{https_ip}:#{https_port}/admin"
  puts "======================================="
end

desc "stop all riak nodes"
task :stop do
  (1..NUM_NODES).each do |n|
    sh %{ulimit -n 4096; ./riak#{n}/bin/riak stop} rescue "not running"
  end
end

desc "restart all riak nodes"
task :restart => [:stop, :start]

desc "join riak nodes (only needed once)"
task :join do
  (2..NUM_NODES).each do |n|
    sh %{./riak#{n}/bin/riak-admin join -f riak1@127.0.0.1} rescue "already joined"
  end
end

desc "clear data from all riak nodes, restart and join"
task :clear => :stop do
  (1..NUM_NODES).each do |n|
    sh %{rm -rf riak#{n}}
    sh %{git checkout riak#{n}}
  end
  [:copy_riak, :start, :join].map { |t| Rake::Task[t].invoke }
end

desc "install riak"
task :install => [:fetch_riak, :make_riak, :copy_riak]

desc "ping all riak nodes"
task :ping do
  (1..NUM_NODES).each do |n|
    sh %{riak#{n}/bin/riak ping}
  end
end

desc "bind riak to all IPs (requires restart)"
task :bind_all do
  (1..NUM_NODES).each do |n|
    system %{sed -i "s/127.0.0.1/0.0.0.0/" riak#{n}/etc/app.config}
  end
end

desc "bind riak to localhost only (requires restart)"
task :bind_local do
  (1..NUM_NODES).each do |n|
    system %{sed -i "s/0.0.0.0/127.0.0.1/" riak#{n}/etc/app.config}
  end
end

task :fetch_riak do
  sh "curl -L #{RIAK_DOWNLOAD_URL} | tar zxvf -" unless File.exist? "riak-#{RIAK_VERSION}"
end

task :make_riak do
  system %{cd riak-#{RIAK_VERSION} && make rel}
end

task :copy_riak do
  (1..NUM_NODES).each do |n|
    system %{cp -nr riak-#{RIAK_VERSION}/rel/riak/* riak#{n}}
  end
end

