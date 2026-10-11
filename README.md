# Configuring Kitty Terminal on CachyOS

A beginner-friendly guide to customizing **Kitty Terminal** with a personal profile picture (PFP), system information, and a personalized terminal appearance using **Fastfetch**.

The goal is simple: create a terminal setup that looks the way you want without unnecessary tools or complicated configuration.

## What You'll Build

- A customized Kitty terminal.
- A personal image displayed beside your system information.
- A Fastfetch layout showing your OS, kernel, CPU, GPU, memory, and more.
- An easy way to change your PFP whenever you want.



## Requirements

This guide is written for CachyOS and assumes you want to use Kitty as your terminal.

| Tool | Purpose |
|---|---|
| [Kitty](https://sw.kovidgoyal.net/kitty/) | Terminal emulator |
| [Fastfetch](https://github.com/fastfetch-cli/fastfetch) | Displays system information and your PFP |
| [ImageMagick](https://imagemagick.org/) | Optional image conversion and resizing |

Install Fastfetch:

```bash
sudo pacman -S fastfetch
```

If Kitty is not installed, install it using CachyOS's available package repositories:

```bash
sudo pacman -S kitty
```

ImageMagick is optional. You do not need it if your image is already in a format that Fastfetch can display.

## 1. Customize Kitty Terminal

Kitty's configuration file controls the terminal's appearance, including fonts, colors, transparency, and other preferences.

Open the configuration file:

```bash
mkdir -p ~/.config/kitty
nano ~/.config/kitty/kitty.conf
```

You can add your preferred settings to this file. For example, to set a font size:

```conf
font_size 13.0
```

Save your changes in Nano:

1. Press `Ctrl + O`.
2. Press `Enter`.
3. Press `Ctrl + X`.

For more configuration options, visit the [official Kitty documentation](https://sw.kovidgoyal.net/kitty/conf/).

## 2. Install and Prepare Your PFP

Create a dedicated directory for your terminal images:

```bash
mkdir -p ~/Pictures/kitty
```

Choose an image from your Downloads folder and copy it into the new directory.

For a PNG image:

```bash
cp ~/Downloads/image.png ~/Pictures/kitty/logo.png
```

For a JPG image:

```bash
cp ~/Downloads/image.jpg ~/Pictures/kitty/logo.jpg
```

Replace `image.png` or `image.jpg` with the actual filename of your image.

**Recommendation:** Use a square image for a profile-picture-style logo. A resolution around 600 × 600 pixels is a reasonable starting point, but it is not mandatory.

### Optional: Convert or Resize Your Image

If you want to convert an image to PNG or resize it, install ImageMagick:

```bash
sudo pacman -S imagemagick
```

Convert JPG to PNG:

```bash
magick ~/Pictures/kitty/logo.jpg ~/Pictures/kitty/logo.png
```

Resize the image while preserving its aspect ratio:

```bash
magick ~/Pictures/kitty/logo.png -resize 600x600 ~/Pictures/kitty/logo.png
```

Skip this section if your image is already suitable.

## 3. Configure Fastfetch

Fastfetch reads its settings from a configuration file.

Generate the default configuration if you do not already have one:

```bash
mkdir -p ~/.config/fastfetch
fastfetch --gen-config
```

Open it:

```bash
nano ~/.config/fastfetch/config.jsonc
```

Replace the existing contents with the following example:

```jsonc
{
  "$schema": "https://github.com/fastfetch-cli/fastfetch/raw/master/doc/json_schema.json",

  "logo": {
    "type": "kitty-direct",
    "source": "/home/your-username/Pictures/kitty/logo.png",
    "width": 22,
    "height": 11,
    "padding": {
      "right": 4
    }
  },

  "modules": [
    "title",
    "separator",
    "os",
    "kernel",
    "shell",
    "cpu",
    "gpu",
    "memory",
    "disk",
    "battery"
  ]
}
```

### Important: Set Your Own Image Path

The example uses `/home/your-username/` as a placeholder. You must replace it with your actual home directory.

Find your home directory by running:

```bash
echo "$HOME"
```

For example, if your home directory is `/home/vector`, your image path should be:

```text
/home/vector/Pictures/kitty/logo.png
```

If you chose `logo.jpg`, change the `source` property to point to that file instead.

Save the configuration using `Ctrl + O`, press `Enter`, and exit with `Ctrl + X`.

### Understanding the Configuration

| Setting | Description |
|---|---|
| `type` | Uses Kitty's direct image-display protocol. |
| `source` | The full path to your PFP image. |
| `width` | Logo width in terminal cells. |
| `height` | Logo height in terminal cells. |
| `padding.right` | Spacing between the image and system information. |
| `modules` | The system information displayed by Fastfetch. |

Adjust `width`, `height`, and `padding.right` to suit your terminal layout.

## 4. Display Your Customized Terminal

Open Kitty and run:

```bash
fastfetch
```

Your image should appear beside your system information.

If the logo does not appear, check the troubleshooting section below.

## 5. Change Your PFP Whenever You Want

You do not need to edit your Fastfetch configuration every time you want a different image.

### Method 1: Replace Your Existing PFP

This is the simplest method. Keep the same filename and directory.

For example, to replace `logo.png` with another PNG image:

```bash
cp ~/Downloads/new-pfp.png ~/Pictures/kitty/logo.png
```

This replaces the existing file with the new image. Your configuration remains unchanged because the image path stays the same.

Run Fastfetch again:

```bash
fastfetch
```

Your new PFP should appear.

### Method 2: Keep Multiple PFPs

If you want to keep multiple images, copy each image into the directory with a different filename:

```bash
cp ~/Downloads/anime-pfp.png ~/Pictures/kitty/anime.png
cp ~/Downloads/minimal-pfp.png ~/Pictures/kitty/minimal.png
```

To switch images, open the configuration:

```bash
nano ~/.config/fastfetch/config.jsonc
```

Change the `source` property to the image you want to display:

```json
"source": "/home/your-username/Pictures/kitty/anime.png"
```

Save the file and run Fastfetch again.

**Tip:** Keeping one permanent filename is the easiest option. Use the second method only if you want to maintain a collection of PFPs.

## 6. Troubleshooting

### The PFP does not appear

Check that the image exists:

```bash
ls -l ~/Pictures/kitty/logo.png
```

If you are using JPG, check `logo.jpg` instead.

### The image path is incorrect

Check your home directory:

```bash
echo "$HOME"
```

Make sure the `source` value points to the actual image file.

### The logo does not display in another terminal

The `kitty-direct` image protocol is intended for Kitty. Run Fastfetch inside Kitty when using this configuration.

### The image looks too large or too small

Adjust the `width` and `height` settings in `config.jsonc`. These values control the logo's displayed size in terminal cells.

### Fastfetch reports a configuration error

Check that the JSONC structure is valid, that the configuration contains no duplicate conflicting settings, and that the image path exists.

You can also consult the [Fastfetch documentation](https://github.com/fastfetch-cli/fastfetch).

## Suggested Repository Structure

If you want to keep your setup organized on GitHub, use a structure like this:

```text
configuring-the-terminal-as-i-want/
├── README.md
├── screenshots/
│   └── kitty-preview.png
└── config/
    ├── kitty.conf
    └── config.jsonc
```

The configuration files are optional examples that readers can inspect or adapt for their own systems. Remove personal information and machine-specific paths before sharing them.

## Useful Resources

- [Kitty Terminal Documentation](https://sw.kovidgoyal.net/kitty/)
- [Fastfetch GitHub Repository](https://github.com/fastfetch-cli/fastfetch)
- [ImageMagick Documentation](https://imagemagick.org/)

## Notes

- This guide is written for CachyOS.
- ImageMagick is optional.
- The PFP image path must match your actual file location.
- Kitty settings and Fastfetch settings are separate configurations.
- Commands and configuration options may vary slightly with software versions.

---

