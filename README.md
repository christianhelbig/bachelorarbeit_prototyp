# Vorbereitungen auf dem NVIDIA Jetson Nano

Bevor die Anwendung auf dem NVIDIA Jetson Nano gestartet werden kann, sind folgende Schritte erforderlich: 

## 0. Grundsätzliche Einrichtung des NVIDIA Jetson Nano

[-> Diese Schritt-für-Schritt Anleitung ausführen](https://developer.nvidia.com/embedded/learn/get-started-jetson-nano-devkit)

## 1. Installation der DeepStream SDK

Für diese Anwendung wird die DeepStream SDK verwendet, welche von NVIDIA bereitgestellt wird. </br>

! _** Es ist wichtig zu beachten, dass die neuste Version der DeepStream SDK nicht kompatibel mit dem NVIDIA Jetson Nano Board ist.  **_ !

Damit diese Anwendung auf dem NVIDIA Jetson Nano ausgeführt werden kann, ist es notwendig, Version 6.0 der DeepStream SDK zu installieren.

[-> Diese Schritt-für-Schritt Anleitung ausführen](https://docs.nvidia.com/metropolis/deepstream/6.0/dev-guide/text/DS_Quickstart.html)

## 2. Installation der Python Bindings

Da diese Anwendung in Python geschrieben wurde, ist es ebenfalls notwendig, die offiziellen Python Bindings zu installieren. Diese findet man in dem [offiziellen GitHub repository](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps/tree/b362b17d24dc91e049b5e0b727f65c556c8a1c6a) von NVIDIA. Hierbei ist es wichtig zu beachten, dass man die Version der Python Bindings installiert, die mit DeepStream 6.0 kompatibel ist. Dies ist Version 1.1.0 und wird in den folgenden Links bereits ausgewählt sein.

Führe zunächste folgende Befehle aus, um Gst Python (ein notwendiges GStreamer Plugin) zu installieren:
```
   $ sudo apt-get install python-gi-dev
   $ export GST_LIBS="-lgstreamer-1.0 -lgobject-2.0 -lglib-2.0"
   $ export GST_CFLAGS="-pthread -I/usr/include/gstreamer-1.0 -I/usr/include/glib-2.0 -I/usr/lib/x86_64-linux-gnu/glib-2.0/include"
   $ git clone https://github.com/GStreamer/gst-python.git
   $ cd gst-python
   $ git checkout 1a8f48a
   $ ./autogen.sh PYTHON=python3
   $ ./configure PYTHON=python3
   $ make
   $ sudo make install
```

[-> Diese Schritt-für-Schritt Anleitung ausführen](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps/blob/b362b17d24dc91e049b5e0b727f65c556c8a1c6a/bindings/README.md). Hierbei reicht es aus, einen Quick build durchzuführen. 

## 3. Installation der Python Beispielanwendungen

Nun werden die Beispielanwendungen geladen, wodurch ebenfalls die notwendige Ordnerstruktur erstellt wird, um diese Anwendung ausführen zu können. 
Die folgenden Schritte ausführen:

Die deepstream_python_apps repo unter <DeepStream 6.0 ROOT>/sources klonen: </br>

```
  $ cd /opt/nvidia/deepstream/deepstream/sources
  $ git clone https://github.com/NVIDIA-AI-IOT/deepstream_python_apps
```

Es wird folgende Struktur erstellt:  
```<DeepStream 6.0 ROOT>/sources/deepstream_python_apps```  

Die Beispielanwendungen befinden sich in dem "apps" Ordner.  

[-> Die Informationen hierfür wurden aus dieser Seite entnommen](https://github.com/NVIDIA-AI-IOT/deepstream_python_apps/blob/b362b17d24dc91e049b5e0b727f65c556c8a1c6a/HOWTO.md)

Anschließend kann dieses Repository auf den NVIDIA Jetson Nano in folgenden Ordner geklont werden: `/opt/nvidia/deepstream/deepstream/sources/deepstream_python_apps/apps/`. Es ist wichtig, diese Anwendung in den erwähnten Ordner zu kopieren, da andernfalls notwendige Python Module nicht gefunden werden.

## 4. Installation von Kafka

Da diese Anwendung eventbasierte Nachrichten nach dem Publish/Subscribe Prinzip mittels Kafka erstellt, muss Kafka installiert und gestartet werden ([zusammengefasst aus dieser Anleitung](https://kafka.apache.org/quickstart)):

[-> lade Apache Kafka herunter](https://www.apache.org/dyn/closer.cgi?path=/kafka/3.3.1/kafka_2.13-3.3.1.tgz)

-> entpacke die Datei und navigiere in den erstellten Ordner 

-> starte den ZooKeeper Service: </br>
```$ bin/zookeeper-server-start.sh config/zookeeper.properties```

-> öffne eine neue Terminal Session und starte den broker </br>
```$ bin/kafka-server-start.sh config/server.properties```

-> öffne eine weitere Terminal Session und erstellen den Channel </br>
```$ bin/kafka-topics.sh --create --topic quickstart-events --bootstrap-server localhost:9092```

-> öffne eine Terminal Session auf dem Gerät, auf dem Kafka Nachrichten angezeigt werden sollen und führe folgenden Befehl (ebenfalls in dem heruntergeladenen kafka Ordner) aus: </br>
```bin/kafka-console-consumer.sh --topic quickstart-events --bootstrap-server localhost:9092```

## Nun kann diese Anwendung gestartet werden

Hierfür muss folgender Befehl ausgeführt werden:

```$ python3 main.py rtsp://...```

Hierbei können mehrere RTSP-Streams angegeben werden.
