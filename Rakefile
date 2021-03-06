require 'rake'
require 'erb'

desc "install the dot files into user's home directory"
task :install do
  install_config_files
  configure_macvim
  configure_git
end


def install_config_files
  replace_all = false

  Dir['*'].each do |file|
    next if %w[Rakefile README.rdoc LICENSE].include? file

    if File.exist?(File.join(ENV['HOME'], ".#{file.sub('.erb', '')}"))
      if File.identical? file, File.join(ENV['HOME'], ".#{file.sub('.erb', '')}")
        puts "identical ~/.#{file.sub('.erb', '')}"
      elsif replace_all
        replace_file(file)
      else
        print "overwrite ~/.#{file.sub('.erb', '')}? [ynaq] "
        case $stdin.gets.chomp
        when 'a'
          replace_all = true
          replace_file(file)
        when 'y'
          replace_file(file)
        when 'q'
          exit
        else
          puts "skipping ~/.#{file.sub('.erb', '')}"
        end
      end
    else
      link_file(file)
    end
  end
end

def replace_file(file)
  system %Q{rm "$HOME/.#{file.sub('.erb', '')}"}
  link_file(file)
end

def link_file(file)
  if file =~ /.erb$/
    puts "generating ~/.#{file.sub('.erb', '')}"
    File.open(File.join(ENV['HOME'], ".#{file.sub('.erb', '')}"), 'w') do |new_file|
      new_file.write ERB.new(File.read(file)).result(binding)
    end
  else
    puts "linking ~/.#{file}"
    system %Q{ln -s "$PWD/#{file}" "$HOME/.#{file}"}
  end
end

# Set the MacVIM window borders to look like the Terminal.app ones.
#
# To restore defaults, use:
#     defaults delete org.vim.MacVim
def configure_macvim
  system("/bin/sh", "-c", <<-EOF)
    defaults write org.vim.MacVim MMTextInsetTop 2
    defaults write org.vim.MacVim MMTextInsetBottom 5
    defaults write org.vim.MacVim MMTextInsetLeft 5
    defaults write org.vim.MacVim MMTextInsetRight 5
    defaults write org.vim.MacVim MMTabOptimumWidth 200
  EOF
end

def configure_git
  system("/bin/sh", "-c", <<-EOF)
    git config --global user.name "Pete Yandell"
    git config --global user.email "pete@elseano.com"
    git config --global github.user elseano
    git config --global difftool.prompt false
    git config --global color.ui true

    # Make git push only push the current branch.
    git config --global push.default tracking

    # Make new branches do a rebase on git pull.
    git config --global branch.autosetuprebase always
    git config --global merge.defaultToUpstream true

    # Helpful aliases.
    git config --global alias.build '!git push -f origin HEAD:build/elseano/$(openssl rand -hex 3)'
    git config --global alias.build-without-master '!git push -f origin HEAD:build/elseano/$(openssl rand -hex 3)-without-master'
    git config --global alias.build-specs '!git push -f origin HEAD:build-specs/elseano/$(openssl rand -hex 3)'
    git config --global alias.build-specs-without-master '!git push -f origin HEAD:build-specs/elseano/$(openssl rand -hex 3)-without-master'
    git config --global alias.build-features '!git push -f origin HEAD:build-features/elseano/$(openssl rand -hex 3)'
    git config --global alias.build-features-without-master '!git push -f origin HEAD:build-features/elseano/$(openssl rand -hex 3)-without-master'
    git config --global alias.build-js '!git push -f origin HEAD:build-js/elseano/$(openssl rand -hex 3)'
    git config --global alias.build-js-without-master '!git push -f origin HEAD:build-js/elseano/$(openssl rand -hex 3)-without-master'
  EOF
end
