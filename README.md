# StevenBenavides-Trabajo-de-consulta-de-virtual-threads

Programa en Java que resuelva el mismo problema que se tomó en el primer parcial, pero, está vez usando virtual threads.

El el prompt que implemente en la IA Gemini es el siguiente:

necesito que me ayudes con un programa al cual debo implementar el uso de Virtual Threads quiero que imagines que eres un programador de Microsoft al cual le dieron la orden de implementar hilos virtuales a un código el cual usa hilos tradiciones quiero que mantengas la lógica en la cual esta hecho el programa el mismo que esta implementado en la versión de java 24 usando el programa Intellij el codigo es el siguiente: "package ec.edu.utpl.carreras.computacion.pga.clases.s1;

import java.io.*;

import java.util.ArrayList;

import java.util.List;



public class UrlAnalyzerApp {

public static void main(String[] args) {

String inputPath = "data/urls_parcial1.txt";

String outputPath = "data/resultados.csv";



List<String> urls = new ArrayList<>();

try (BufferedReader reader = new BufferedReader(new FileReader(inputPath))) {

String line;

while ((line = reader.readLine()) != null) {

urls.add(line.trim());

}

} catch (IOException e) {

System.out.println("Error leyendo archivo: " + e.getMessage());

return;

}



List<UrlAnalyzer> analyzers = new ArrayList<>();

List<Thread> threads = new ArrayList<>();



for (String url : urls) {

UrlAnalyzer analyzer = new UrlAnalyzer(url);

Thread t = new Thread(analyzer);

threads.add(t);

analyzers.add(analyzer);

t.start();

}



// Esperar a que todos los hilos terminen

for (Thread t : threads) {

try {

t.join();

} catch (InterruptedException e) {

System.out.println("Hilo interrumpido: " + e.getMessage());

}

}



// Escribir archivo CSV

try (PrintWriter writer = new PrintWriter(new FileWriter(outputPath))) {

writer.println("URL,NumURLsInternas");

for (UrlAnalyzer analyzer : analyzers) {

writer.printf("%s,%d%n", analyzer.getUrl(), analyzer.getInternalLinks());

}

} catch (IOException e) {

System.out.println("Error escribiendo archivo: " + e.getMessage());

}



System.out.println("Proceso terminado. Revisa el archivo: " + outputPath);

}

} package ec.edu.utpl.carreras.computacion.pga.clases.s1;



import org.jsoup.Jsoup;

import org.jsoup.nodes.Document;

import org.jsoup.nodes.Element;

import org.jsoup.select.Elements;



import java.net.URL;



public class UrlAnalyzer implements Runnable {

private final String url;

private int internalLinks = 0;



public UrlAnalyzer(String url) {

this.url = url;

}



@Override

public void run() {

try {

Document doc = Jsoup.connect(url).get();

URL base = new URL(url);

String host = base.getHost();



Elements links = doc.select("a[href]");

for (Element link : links) {

String absHref = link.absUrl("href");

if (!absHref.isEmpty()) {

URL linkUrl = new URL(absHref);

if (linkUrl.getHost().equalsIgnoreCase(host)) {

internalLinks++;

}

}

}



} catch (Exception e) {

System.out.println("Error procesando " + url + ": " + e.getMessage());

}

}



public String getUrl() {

return url;

}



public int getInternalLinks() {

return internalLinks;

}

}" 
a este código que te proporciono quiero que le implementes lo antes mencionado y como resultado quiero que me des 3 versiones del código en el que implementes los hilos virtuales también quiero que comentes las lineas en las que se hizo la implementación y dame una explicación porque lo implementaste de esa manera.

``` Java 
package ec.edu.utpl.carreras.computacion.pga.clases.s1;

import java.io.*;
import java.util.ArrayList;
import java.util.List;

public class UrlAnalyzerApp {
    public static void main(String[] args) {
        String inputPath = "data/urls_parcial1.txt";
        String outputPath = "data/resultados.csv";

        List<String> urls = new ArrayList<>();
        try (BufferedReader reader = new BufferedReader(new FileReader(inputPath))) {
            String line;
            while ((line = reader.readLine()) != null) {
                urls.add(line.trim());
            }
        } catch (IOException e) {
            System.out.println("Error leyendo archivo: " + e.getMessage());
            return;
        }

        List<UrlAnalyzer> analyzers = new ArrayList<>();
        List<Thread> threads = new ArrayList<>();

        for (String url : urls) {
            UrlAnalyzer analyzer = new UrlAnalyzer(url);
            analyzers.add(analyzer);
            // *** CAMBIO CLAVE 1: Creación de un Virtual Thread ***
            // Se utiliza Thread.ofVirtual() para crear un nuevo hilo virtual.
            // Los hilos virtuales son mucho más ligeros que los hilos de plataforma,
            // permitiendo la creación de un gran número de ellos sin sobrecargar el sistema.
            Thread t = Thread.ofVirtual().start(analyzer);
            threads.add(t);
        }

        // Esperar a que todos los hilos virtuales terminen
        // La lógica de join() sigue siendo la misma para la espera de hilos.
        for (Thread t : threads) {
            try {
                t.join();
            } catch (InterruptedException e) {
                System.out.println("Hilo interrumpido: " + e.getMessage());
            }
        }

        // Escribir archivo CSV
        try (PrintWriter writer = new PrintWriter(new FileWriter(outputPath))) {
            writer.println("URL,NumURLsInternas");
            for (UrlAnalyzer analyzer : analyzers) {
                writer.printf("%s,%d%n", analyzer.getUrl(), analyzer.getInternalLinks());
            }
        } catch (IOException e) {
            System.out.println("Error escribiendo archivo: " + e.getMessage());
        }

        System.out.println("Proceso terminado. Revisa el archivo: " + outputPath);
    }
}
```

``` Java
package ec.edu.utpl.carreras.computacion.pga.clases.s1;

import org.jsoup.Jsoup;
import org.jsoup.nodes.Document;
import org.jsoup.nodes.Element;
import org.jsoup.select.Elements;

import java.net.URL;

public class UrlAnalyzer implements Runnable {
    private final String url;
    private int internalLinks = 0;

    public UrlAnalyzer(String url) {
        this.url = url;
    }

    @Override
    public void run() {
        try {
            Document doc = Jsoup.connect(url).get();
            URL base = new URL(url);
            String host = base.getHost();

            Elements links = doc.select("a[href]");
            for (Element link : links) {
                String absHref = link.absUrl("href");
                if (!absHref.isEmpty()) {
                    URL linkUrl = new URL(absHref);
                    if (linkUrl.getHost().equalsIgnoreCase(host)) {
                        internalLinks++;
                    }
                }
            }

        } catch (Exception e) {
            System.out.println("Error procesando " + url + ": " + e.getMessage());
        }
    }

    public String getUrl() {
        return url;
    }

    public int getInternalLinks() {
        return internalLinks;
    }
}
```

