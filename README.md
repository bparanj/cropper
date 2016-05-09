
gem 'rmagick'
gem 'carrierwave'

brew install imagemagick

rails generate uploader Avatar

class User < ApplicationRecord
  mount_uploader :avatar, AvatarUploader
  
end

rails db:migrate

rails s

Create an user with an image.

Version thumb doesn't exist!

Add:

version :thumb do
  resize_to_fill(100, 100)
end

to avatar_uploader.rb

undefined method `resize_to_fill' for Class


Uncomment:

  include CarrierWave::RMagick
  
in avatar_uploader.rb.

