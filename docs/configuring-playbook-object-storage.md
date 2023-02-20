# Configuring Remote Storage

PeerTube media files can be stored on [Object (Remote) Storage](https://docs.joinpeertube.org/admin-remote-storage) using a service like [AWS S3](https://aws.amazon.com/s3/) or compatible alternative ([Backblaze B2](https://www.backblaze.com/b2/cloud-storage.html), [Wasabi](https://wasabi.com), [Digital Ocean Spaces](https://www.digitalocean.com/products/spaces), etc).


## How does it work?

Media files are initially stored on the server's filesystem and only moved to the Object Storage provider after [transcoding](https://en.wikipedia.org//wiki/Transcoding) completes.

Depending on the video's privacy settings (public, private, unlisted, internal), a different ACL is applied to the object in the bucket.

For `public` and `unlisted` videos, the ACL is `public-read`. When serving the video, PeerTube directs users to the S3 endpoint so they can fetch it directly from there.

For `private` and `unlisted` videos, objects remain private and can't be streamed from the S3 endpoint directly. They go through a PeerTube proxy URL.


## Preparing the bucket

Your bucket can be `private`


## Configuration

Add this to your configuration file (`inventory/host_vars/<your-peertube-hostname>/vars.yml`):

```yaml
peertube_config_object_storage_enabled: true
peertube_config_object_storage_endpoint: s3.REGION.amazonaws.com
peertube_config_object_storage_region: REGION

peertube_config_object_storage_credentials_access_key_id: ''
peertube_config_object_storage_credentials_secret_access_key: ''

peertube_config_object_storage_streaming_playlists_bucket_name: BUCKET_NAME
peertube_config_object_storage_streaming_playlists_prefix: streaming-playlists/

peertube_config_object_storage_videos_bucket_name: BUCKET_NAME
peertube_config_object_storage_videos_prefix: videos/
```

Replace `REGION` with your AWS S3 bucket's region, `BUCKET_NAME` with the name of a bucket.

The configuration above uses the same bucket (`BUCKET_NAME`) for both types of video-files potentially managed by PeerTube (`hls` streaming files and `webtorrent` video files). The files are prefixed in the bucket, so there's no conflict. You could remove the prefix and use separate buckets for each type of video file.

Don't forget to populate the access key and secret. The IAM user corresponding to this access key must have write access to the bucket.

For non-AWS-S3 depoyments (when using other Object Storage providers), you'll need to adjust `peertube_config_object_storage_endpoint` as well.
