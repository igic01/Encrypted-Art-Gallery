# Encrypted Art Gallery

A Node.js + Express gallery app where artists upload PNGs, encrypt them client-side, and share them as previewable posts. Visitors can try to decrypt an image with the correct password and download the result.

## Demo video
- [Demo (starts at 10s)](Video_Gallery_proj.mp4#t=10)

## Features
- User registration and login with session support
- Artist profile page with bio and uploaded works
- Client-side PNG encryption and upload as a `.dat` JSON payload
- Server-side rendering of a decrypted preview for the public gallery
- Per-photo decryption attempt on the photo page, with download as PNG
- Delete uploaded photos and associated files

## Tech stack
- Node.js, Express, EJS
- MongoDB (local)
- Multer for uploads
- Canvas for server-side image decoding

## How it works
- The browser encrypts half of the pixels of a PNG using a password-derived byte key and saves the result as a JSON `.dat` file.
- The server stores the `.dat` file in `uploads/`, decrypts the pixels to create a PNG preview in `uploads_dec/`, and records the photo name in MongoDB.
- The photo page fetches the `.dat` file, tries to decrypt it with the provided password, and lets the user download the result.

## XOR encryption/decryption
The image encryption uses a simple XOR pass over the RGB channels starting at a mid-point pixel. XOR is symmetric, so applying the same operation with the same key both encrypts and decrypts the data.

Encryption in `views_enchantments/script/encrypt.js`:
```js
for (let i = encryptStart * 4; i < data.length; i += 4) {
    data[i] = data[i] ^ encryptionKey;
    data[i + 1] = data[i + 1] ^ encryptionKey;
    data[i + 2] = data[i + 2] ^ encryptionKey;
}
```

Decryption in `views_enchantments/script/photo.js`:
```js
for (let i = encryptStartPixel * 4; i < pixels.length; i += 4) {
    pixels[i] = pixels[i] ^ decryptionKey;
    pixels[i + 1] = pixels[i + 1] ^ decryptionKey;
    pixels[i + 2] = pixels[i + 2] ^ decryptionKey;
}
```

## Getting started
1. Install dependencies:
   - `npm install`
2. Start MongoDB locally (default: `mongodb://localhost:27017`).
3. Run the server:
   - `npm start`
   - `node server.js`
4. Open the app:
   - `http://localhost:3000`

## Environment and config
- MongoDB URL: `mongodb://localhost:27017` (configured in `do_connection.js`)
- Database name: `db_gallery`
- Server port: `3000` (set in `server.js`)

## Routes
### Pages (GET)
- `/` - Gallery feed
- `/login` - Login form
- `/register` - Registration form
- `/my_page` - Authenticated user page
- `/artist/:artist_name` - Public artist profile
- `/photo/:photo_name` - Photo page with decryption UI
- `/logout` - Destroy session and redirect

### Actions (POST)
- `/register` - Create user account
- `/login` - Create session
- `/update_user_info` - Update profile bio
- `/upload` - Upload encrypted `.dat` file
- `/delete_photo` - Remove photo and associated files

## Project structure
- `server.js` - Express server setup and session config
- `routes.js` - Page routes (gallery, login, register, artist, photo)
- `db.js` - Auth, upload, encryption/decryption, and photo management
- `do_connection.js` - MongoDB connection helpers
- `views/` - EJS templates
- `views_enchantments/` - Static CSS/JS assets (encryption and decryption logic)
- `uploads/` - Encrypted `.dat` uploads
- `uploads_dec/` - Decrypted PNG previews

## Notes
- MongoDB database name: `db_gallery`
- Default port: `3000`

