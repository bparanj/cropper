
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

  version :large do
    resize_to_limit(600, 600)
  end

  def crop
    if model.crop_x.present?
      resize_to_limit(600, 600)
      manipulate! do |img|
        x = model.crop_x.to_i
        y = model.crop_y.to_i
        w = model.crop_w.to_i
        h = model.crop_h.to_i
        img.crop!(x, y, w, h)
      end
    end
  end

in avatar_uploader.rb.

  def update
    @user = User.find(params[:id])
    if @user.update_attributes(allowed_params)
      if params[:user][:avatar].present?
        render :crop
      else
        redirect_to @user, notice: "Successfully updated user."
      end
    else
      render :new
    end
  end

  def create
    @user = User.new(allowed_params)
    if @user.save
      if params[:user][:avatar].present?
        render :crop
      else
        redirect_to @user, notice: "Successfully created user."
      end
    else
      render :new
    end
  end

<h1>Crop Avatar</h1>

<%= image_tag @user.avatar_url(:large), id: "cropbox" %>

<h4>Preview</h4>
<div style="width:100px; height:100px; overflow:hidden">
  <%= image_tag @user.avatar.url(:large), :id => "preview" %>
</div>

<%= form_for @user do |f| %>
  <% %w[x y w h].each do |attribute| %>
    <%= f.hidden_field "crop_#{attribute}" %>
  <% end %>
  <div class="actions">
    <%= f.submit "Crop" %>
  </div>
<% end %>

User

  attr_accessor :crop_x, :crop_y, :crop_w, :crop_h
  after_update :crop_avatar
  
  def crop_avatar
    avatar.recreate_versions! if crop_x.present?
  end

Copy jquery.Jcrop.css to vendor/assets/stylesheets
jquery.Jcrop.js to vendor/assets/javascripts

//= require jquery.Jcrop

in application.js

 *= require jquery.Jcrop
 
in application.css


jQuery ->
  new AvatarCropper()

class AvatarCropper
  constructor: ->
    $('#cropbox').Jcrop
      aspectRatio: 1
      setSelect: [0, 0, 600, 600]
      onSelect: @update
      onChange: @update
  
  update: (coords) =>
    $('#user_crop_x').val(coords.x)
    $('#user_crop_y').val(coords.y)
    $('#user_crop_w').val(coords.w)
    $('#user_crop_h').val(coords.h)
    @updatePreview(coords)

  updatePreview: (coords) =>
  	$('#preview').css
  		width: Math.round(100/coords.w * $('#cropbox').width()) + 'px'
  		height: Math.round(100/coords.h * $('#cropbox').height()) + 'px'
  		marginLeft: '-' + Math.round(100/coords.w * coords.x) + 'px'
  		marginTop: '-' + Math.round(100/coords.h * coords.y) + 'px'

users.coffee

