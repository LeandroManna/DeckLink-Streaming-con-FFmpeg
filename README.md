# Transmisión de Video con FFmpeg

Este repositorio contiene ejemplos de comandos de FFmpeg para la transmisión de video utilizando el dispositivo DeckLink Mini Recorder como entrada. Los comandos incluyen ajustes específicos de codificación de video y audio para la transmisión en vivo a través de RTMP.

## Código Original

El siguiente comando de FFmpeg captura el video del dispositivo DeckLink Mini Recorder y lo transmite a través de RTMP:

```bash
ffmpeg -re -rtbufsize 2105M -f decklink -i "DeckLink Mini Recorder" -pix_fmt yuv420p -r 23.976 -vcodec libx264 -vf "scale=1280:720" -b:v 3000k -preset ultrafast -profile:v baseline -keyint_min 24 -g 48 -x264opts no-scenecut -acodec mp3 -b:a 96k -af "aresample=async=1:min_hard_comp=0.100000:first_pts=0" -map_metadata -1 -f flv "rtmp://IP:PUERTO/RUTA/NOMBRE"
```

Explicación de los parámetros:

- `-re`: Reproducir el archivo de entrada a velocidad en tiempo real.
- `-rtbufsize 2105M`: Tamaño del búfer en tiempo real.
- `-f decklink -i "DeckLink Mini Recorder"`: Captura el video del dispositivo DeckLink Mini Recorder.
- `-pix_fmt yuv420p`: Formato de píxeles del video de salida.
- `-r 23.976`: Tasa de fotogramas de salida.
- `-vcodec libx264`: Códec de video utilizado para la codificación.
- `-vf "scale=1280:720"`: Escala el video de salida a 1280x720 píxeles.
- `-b:v 3000k`: Tasa de bits de video.
- `-preset ultrafast`: Velocidad de codificación ultra rápida.
- `-profile:v baseline`: Perfil de codificación de video.
- `-keyint_min 24`: Intervalo mínimo entre fotogramas clave.
- `-g 48`: Frecuencia de las imágenes clave.
- `-x264opts no-scenecut`: Deshabilita la detección automática de cambios de escena.
- `-acodec mp3 -b:a 96k`: Códec de audio utilizado para la codificación, con una tasa de bits de 96k.
- `-af "aresample=async=1:min_hard_comp=0.100000:first_pts=0"`: Ajustes de audio para resampleo y otros parámetros.
- `-map_metadata -1`: Evita copiar la metadata del archivo de entrada.
- `-f flv "rtmp://IP:PUERTO/RUTA/NOMBRE"`: URL de destino para la transmisión RTMP.

## Código con Filtro de Tono

El siguiente comando agrega un tono sinusoidal de 18000 Hz al audio original del video, priorizando el tono sobre el audio original en la mezcla:

```bash
ffmpeg -re -rtbufsize 2105M -f decklink -i "DeckLink Mini Recorder" -f lavfi -i "sine=frequency=18000:sample_rate=48000" -pix_fmt yuv420p -r 23.976 -vcodec libx264 -vf "scale=1280:720" -b:v 3000k -preset ultrafast -profile:v baseline -keyint_min 24 -g 48 -x264opts no-scenecut -acodec aac -b:a 96k -filter_complex "[0:a][1:a]amix=inputs=2:duration=first:dropout_transition=2:weights=10.0 10.0[a]" -map 0:v:0 -map "[a]" -shortest -y -f flv "rtmp://IP:PUERTO/RUTA/NOMBRE"
```

Explicación de los parámetros adicionales:

- `-f lavfi -i "sine=frequency=18000:sample_rate=48000"`: Genera un tono sinusoidal de 18000 Hz como entrada de audio.
- `-filter_complex "[0:a][1:a]amix=inputs=2:duration=first:dropout_transition=2:weights=10.0 10.0[a]"`: Combina el audio del video original con el tono generado, ajustando el volumen del tono para que tape el audio original.

---

Recuerda reemplazar `IP:PUERTO/RUTA/NOMBRE` con la dirección IP, puerto, ruta y nombre de destino correspondientes para tu transmisión RTMP.