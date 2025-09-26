# Home Assistant Blueprints
A collection of custom built blueprints for my Home Assistant Install

## Ring MQTT Video Download LLM Vision Notifications
Triggers on Motion detection on the camera, downloads the associated video clip and runs it through [LLM Vision](https://github.com/valentinfrlch/ha-llmvision) Video Analyzer, adds to the timeline and notifies. This is inspired by the timeline blueprint provided by LLM Vision which I couldn't get working nicely with Ring due to videos being remote and significant delays when starting a stream.

This blueprint makes use of the [RingMQTT](https://github.com/tsightler/ring-mqtt) addon for the Camera integration.

You will also need to configure the [download integration](https://www.home-assistant.io/integrations/downloader/) built into HA which is then part of the configuration for the blueprint.

Finally you will need to add the following shell script to your `configuation.yaml` alongside the following media paths
```yaml
homeassistant:
  media_dirs:
    local: /media
    llmvision: /config/media/llmvision
    ring: /config/media/ring_cams
recorder:
  purge_keep_days: 180

shell_command:
  make_motion_gif_hq: >-
    bash -lc 'ffmpeg -y -ss {{ start|default(0) }} -t {{ duration|default(5) }} -i {{ input }} -filter_complex "[0:v]fps={{ fps|default(8) }},scale={{ width|default(480) }}:-1:flags=lanczos,split[v0][v1];[v0]palettegen=max_colors={{ colors|default(128) }}:stats_mode=full[p];[v1][p]paletteuse=dither={{ dither|default(sierra2_4a) }}:diff_mode=rectangle[out]" -map "[out]" -an -loop 0 {{ output }}'

```
