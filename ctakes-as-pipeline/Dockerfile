FROM openjdk:8-alpine

RUN apk update && apk add ca-certificates openssl wget unzip

## ~1.7G
RUN wget -q -O - http://apache.osuosl.org//ctakes/ctakes-4.0.0/apache-ctakes-4.0.0-bin.tar.gz | tar xzf -
## ~76M
RUN wget -q -O - http://archive.apache.org/dist/uima//uima-as-2.9.0/uima-as-2.9.0-bin.tar.gz | tar xzf -

## 12 MB zipped, ~84MB unzipped
RUN wget -O dict.zip https://downloads.sourceforge.net/project/ctakesresources/sno_rx_16ab.zip
RUN unzip -o dict.zip -d apache-ctakes-4.0.0/resources/org/apache/ctakes/dictionary/lookup/fast/

RUN mkdir /desc

COPY deployAsyncService.sh /
COPY desc/deploymentDescriptor.xml /desc/
COPY desc/dictionaryPipeline.xml /desc/
COPY DeidAwareTermConsumer.java /
COPY lookup-descriptor-template.xml /apache-ctakes-4.0.0/resources/org/apache/ctakes/dictionary/lookup/fast/sno_rx_16ab.xml

RUN javac -cp /apache-ctakes-4.0.0/lib/uimaj-core-2.9.0.jar:/apache-ctakes-4.0.0/lib/ctakes-dictionary-lookup-fast-4.0.0.jar:/apache-ctakes-4.0.0/lib/ctakes-core-4.0.0.jar:/apache-ctakes-4.0.0/lib/ctakes-type-system-4.0.0.jar:/apache-ctakes-4.0.0/lib/uimafit-core-2.2.0.jar DeidAwareTermConsumer.java
RUN jar cf consumer.jar DeidAwareTermConsumer.class

ENV UIMA_HOME=/apache-uima-as-2.9.0
ENV CTAKES_HOME=/apache-ctakes-4.0.0

CMD ./deployAsyncService.sh desc/deploymentDescriptor.xml -brokerURL "tcp://$broker_host:$broker_port"
