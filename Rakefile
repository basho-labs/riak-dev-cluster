PRODUCT       = "kv" #options: kv, ts
VERSION       = "2.1.4"
BACKEND       = 'leveldb' #options: leveldb, bitcask, memory; (setting ignored in ts, only leveldb supported)
NUM_NODES     = 5
RING_SIZE     = 16

VERSION_MAJOR = "#{VERSION}".split('.')[0]
VERSION_MINOR = "#{VERSION}".split('.')[1]
QUAL_D = case PRODUCT
	when "kv"
		""
	when "ts"
		"-ts"
	end
QUAL_U = case PRODUCT
	when "kv"
		""
	when "ts"
		"_ts"
	end
DOWNLOAD_URL  = "http://s3.amazonaws.com/downloads.basho.com/riak#{QUAL_U}/#{VERSION_MAJOR}.#{VERSION_MINOR}/#{VERSION}/osx/10.8/riak#{QUAL_D}-#{VERSION}-OSX-x86_64.tar.gz"


task :default => :help

task :help do
  sh %{rake -T}
end

desc "install, start, and join riak nodes"
task :bootstrap => [:install, :start, :join]

desc "install riak"
task :install => [:fetch_riak, :copy_riak]

task :fetch_riak do
	begin
  	sh "curl -L #{DOWNLOAD_URL} | tar xz -" unless File.exist? "riak#{QUAL_D}-#{VERSION}"
  rescue
  	puts "Command aborted: download failed from #{DOWNLOAD_URL}"
  	abort
  end
end

task :copy_riak do
  (1..NUM_NODES).each do |n|
    system %{cp -nr riak#{QUAL_D}-#{VERSION}/ riak#{n}}
    system %(sed -i '' 's/riak@127.0.0.1/riak#{n}@127.0.0.1/' riak#{n}/etc/riak.conf)
    system %(sed -i '' 's/127.0.0.1:8098/127.0.0.1:1#{n}098/' riak#{n}/etc/riak.conf)
    system %(sed -i '' 's/127.0.0.1:8087/127.0.0.1:1#{n}087/' riak#{n}/etc/riak.conf)
    system %(echo 'riak_control = on' >> riak#{n}/etc/riak.conf)
    system %(echo 'handoff.port = 1#{n}099' >> riak#{n}/etc/riak.conf)
    system %(echo 'ring_size = #{RING_SIZE}' >> riak#{n}/etc/riak.conf)
    system %(echo 'storage_backend = #{BACKEND}' >> riak#{n}/etc/riak.conf) unless PRODUCT == "ts"
  end
end

desc "start all riak nodes"
task :start do
  (1..NUM_NODES).each do |n|
  	begin
	    sh %{ulimit -n 65536; ./riak#{n}/bin/riak start}
	  rescue
  		puts "Command aborted: start failed for node #{n}"
  		abort
  	end
  end
  puts "========================================"
  puts "Riak Dev Cluster started"
  puts
  puts "HTTP API: http://127.0.0.1:11098"
  puts
  puts "Admin UI: http://127.0.0.1:11098/admin"
  puts "========================================"
end

desc "stop all riak nodes"
task :stop do
  (1..NUM_NODES).each do |n|
    sh %{ulimit -n 65536; ./riak#{n}/bin/riak stop} rescue "not running"
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

desc "clear data from all riak nodes"
task :clear => :stop do
  (1..NUM_NODES).each do |n|
    sh %{rm -rf riak#{n}} rescue "remove failed"
  end
end

desc "ping all riak nodes"
task :ping do
  (1..NUM_NODES).each do |n|
    sh %{riak#{n}/bin/riak ping} rescue "ping failed"
  end
end

desc "riak-admin test"
task :test do
  (1..NUM_NODES).each do |n|
    sh %{riak#{n}/bin/riak-admin test} rescue "test failed"
  end
end

desc "riak-admin member-status"
task :member_status do
  sh %{riak2/bin/riak-admin member-status} rescue puts "Command aborted"
end

desc "riak-admin status"
task :status do
  sh %{riak1/bin/riak-admin status} rescue puts "Command aborted"
end

desc "riak-admin ring-status"
task :ring_status do
  sh %{riak1/bin/riak-admin ring-status} rescue puts "Command aborted"
end

desc "set up maps bucket-type"
task :map_bucket do
	if PRODUCT == "kv"
  	sh %{riak1/bin/riak-admin bucket-type create maps '{"props":{"datatype":"map"}}'}
  	sh %{riak1/bin/riak-admin bucket-type activate maps}
  else
  	puts "Command aborted: CRDTs are not supported in Riak Time-Series"
  end
end

desc "set up sets bucket-type"
task :set_bucket do
	if PRODUCT == "kv"
  	sh %{riak1/bin/riak-admin bucket-type create sets '{"props":{"datatype":"set"}}'}
  	sh %{riak1/bin/riak-admin bucket-type activate sets}
  else
  	puts "Command aborted: CRDTs are not supported in Riak Time-Series"
  end
end

desc "set up counters bucket-type"
task :counter_bucket do
	if PRODUCT == "kv"
  	sh %{riak1/bin/riak-admin bucket-type create counters '{"props":{"datatype":"counter"}}'}
  	sh %{riak1/bin/riak-admin bucket-type activate counters}
  else
  	puts "Command aborted: CRDTs are not supported in Riak Time-Series"
  end
end

