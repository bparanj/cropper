
gem 'rmagick'
gem 'carrierwave'

brew install imagemagick

rails generate uploader Avatar

class User < ApplicationRecord
  mount_uploader :avatar, AvatarUploader
  
end

