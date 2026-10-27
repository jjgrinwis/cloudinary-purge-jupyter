# Cloudinary Video Purge Notebook

This project contains a Jupyter notebook for selecting an uploaded Cloudinary
video, purging its Akamai cache tag, and deleting the video from Cloudinary.

The notebook uses the video's Cloudinary `public_id` as the Akamai cache tag.
The Akamai purge targets the `staging` network.

## Requirements

- macOS or Linux
- Python 3.13 or newer
- Cloudinary Admin API credentials
- Akamai EdgeGrid credentials with purge permissions

## Install `uv`

`uv` manages the Python version, virtual environment, and project dependencies.
Install it with the official installer:

```bash
curl -LsSf https://astral.sh/uv/install.sh | sh
```

Restart the terminal or reload your shell configuration, then verify the
installation:

```bash
uv --version
```

## Set Up the Project

From the project directory, install the declared dependencies and create the
project environment:

```bash
uv sync
```

Start Jupyter with the project environment:

```bash
uv run jupyter notebook purge_video.ipynb
```

You can use JupyterLab instead:

```bash
uv run jupyter lab purge_video.ipynb
```

## Configure Credentials

Set the Cloudinary Admin API variables in the same shell used to start Jupyter:

```bash
export CLOUDINARY_CLOUD_NAME="your-cloud-name"
export CLOUDINARY_API_KEY="your-api-key"
export CLOUDINARY_API_SECRET="your-api-secret"
```

The notebook reads Akamai EdgeGrid credentials from `~/.edgerc`. Select a
section with:

```bash
export AKAMAI_EDGEGRID_SECTION="default"
```

The section defaults to `default` when the variable is omitted. The `.edgerc`
section must contain the EdgeGrid `host` and credentials, and the API user must
have purge permissions.

## Use the Notebook

Run the cells in order:

1. Configure the Akamai EdgeGrid session.
2. List up to 100 uploaded Cloudinary videos.
3. Enter the number of the video to select it.
4. Type `DELETE` to confirm.

The final cell sends the selected `public_id` as a cache tag to:

```text
POST /ccu/v3/invalidate/tag/staging
```

After Akamai accepts the purge request, the notebook deletes the selected
Cloudinary video with `delete_resources` and invalidates Cloudinary delivery
caches.

The operation is destructive. Keep API secrets out of the notebook and do not
commit them to source control.
