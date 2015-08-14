require 'pathname'

task default: 'docker:build'

namespace :docker do
  image_name = 'feduxorg/centos-apache'
  container_name = 'centos-apache1'

  desc 'Build docker image'
  task :build, :nocache do |_, args|
    nocache = args[:nocache]

    cmdline = []
    cmdline << 'docker'
    cmdline << 'build'
    cmdline << '--no-cache=true' if nocache
    cmdline << "-t #{image_name}"
    cmdline << '.'

    Dir.chdir 'files/latest' do
      sh cmdline.join(' ')
    end
  end

  desc 'Run docker container'
  task :run, :command do |_, task_args|
    command = task_args[:command]

    mkdir_p %w(var/ssh/authorized_keys var/ssh/host_keys)

    args =[]
    args << '-it'
    args << '--rm'
    args << "--name #{container_name}"
    args << "-p 8022:22"
    args << "-v /sys/fs/cgroup:/sys/fs/cgroup"
    args << "-v #{File.expand_path('var/ssh')}:/var/ssh/" if File.directory? 'var/ssh'
    # args << "-v /var/log/journal:/var/log/journal" if File.directory? '/var/log/journal'

    cmdline = []
    cmdline << 'docker'
    cmdline << 'run'
    cmdline.concat args
    cmdline << image_name
    cmdline << command if command

    sh cmdline.join(' ')
  end
end

task :clean do
  sh 'sudo rm -rf tmp/*'
end

namespace :templates do
  desc 'Copy templates'
  task :copy do
    Rake::FileList.new('files/**/*').select { |f| File.directory? f }.each do |d|
      files = Rake::FileList.new("templates/root/*").to_a
      files += Rake::FileList.new("templates/root/.???*").to_a
      FileUtils.cp(files, d)

      type = File.basename(File.dirname(d))
      files = Rake::FileList.new("templates/#{type}/*").to_a
      files += Rake::FileList.new("templates/#{type}/.???*").to_a
      FileUtils.cp(files, d)
    end
  end
end
