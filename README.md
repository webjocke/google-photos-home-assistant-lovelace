# Google Photos Home Assistant Lovelace carousel
Shows a automatic slideshow of images from a Google Photos album.

There is two steps, a rest sensor that gets all the recent images in the album and a lovelace card that shows the images.

# Installation
### Add this rest sensor to your configuration.yaml file.
```
sensor:
  - platform: rest
      resource: https://www.publicalbum.org/api/v2/webapp/embed-player/jsonrpc
      name: My Cats
      method: POST
      headers:
        accept: application/json
      payload: '{"method":"getGooglePhotosAlbum","params":{"sharedLink":"<YOUR-ALBUM-LINK>","imageWidth":1920,"imageHeight":1080,"includeThumbnails":false,"videoQuality":"1080p","attachMetadata":false},"id":1}'
      json_attributes_path: "$.result"
      json_attributes:
        - mediaItems
      value_template: "{{ value_json.result.title }}"
      scan_interval: 3600
```
Replace `<YOUR-ALBUM-LINK>` with the share link of your album. To get your link, go to your Google Photos album, click share and choose link. The link looks like `https://photos.app.goo.gl/F9FCuFP3CPhgKqjgK`. Feel free to change the sensor name to your liking, it will be translated to a sensor id (sensor.my_cats in this case).

### Download the `google-photos.js` file to `config/www/` on your home assistant server.
- Go to Configuration -> Lovelace Controlpanels -> Resources
- Add resource `/local/google-photos.js` with type Javascript Module
- Go to a panel and add a random lovelace card (for example the button card) and click `Show code editor`
Replace all the text with the following content.
```
type: custom:cats-card
entity: sensor.my_cats
```
Replace `sensor.my_cats` with your sensor id in case you changed the name of the sensor.

# Info
- The card will show up to 10 most recent images in the album.
- `scan_interval` is how many seconds between fetches to google photos. Basically 3600 seconds means it takes up to 1 hours from the point when you add a new photo to the album until it shows up in the slideshow.
- If you wants multiple slideshows feel free to create two sensors (but with different names) and add two cards pointing to different sensors.
- The sensor uses a public api from publicalbum.org to get all photos in a Google Photos album, I have no idea how long time this api will be online. publicalbum.org can potensioally see all album links that gets send though them (and they can then see all the photos), so if your album containes really private images/videos, don't use this.
