source "https://rubygems.org"

bitclust_path = ENV.fetch('BITCLUST_PATH', '../bitclust')
if File.directory?(bitclust_path)
  path bitclust_path do
    gem "bitclust-core"
  end
else
  git 'https://github.com/rurema/bitclust.git' do
    gem "bitclust-core"
  end
end
