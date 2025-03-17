package act1;

public class Ejercicio1 {
    public static void main(String[] args) {
        int num1 = 10;
        double num2 = 5.5;
        String mensaje = "El resultado de la suma es: ";

        double resultado = num1 + num2;
        String resultadoStr = String.valueOf(resultado);
        String mensajeCompleto = "Resultado final: " + resultadoStr + " calculado correctamente.";

        System.out.println(mensaje + resultado);
        System.out.println(mensajeCompleto.replace("calculado", "procesado"));
        System.out.println("Substring del resultado: " + resultadoStr.substring(0, Math.min(5, resultadoStr.length())));
        System.out.println("¿El mensaje contiene un número? " + mensajeCompleto.matches(".*\\d+.*"));
    }
}


package act2;

import java.util.*;

public class Ejercicio2 {
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        List<Integer> numeros = new ArrayList<>();
        
        System.out.println("Ingrese 5 números enteros:");
        for (int i = 0; i < 5; i++) numeros.add(scanner.nextInt());
        scanner.close();
        
        int suma = numeros.stream().mapToInt(Integer::intValue).sum();
        int max = Collections.max(numeros), min = Collections.min(numeros);
        double promedio = (double) suma / numeros.size();
        
        Collections.sort(numeros);
        
        System.out.println("Suma: " + suma + " | Promedio: " + promedio);
        System.out.println("Máximo: " + max + " | Mínimo: " + min);
        System.out.println("Lista ordenada: " + numeros);
    }
}

package act3;

import java.io.*;
import java.util.*;
import org.w3c.dom.*;
import javax.xml.parsers.*;
import javax.xml.transform.*;
import javax.xml.transform.dom.DOMSource;
import javax.xml.transform.stream.StreamResult;

public class Ejercicio3 {
    static final String ARCHIVO = "datos.xml";
    
    public static void main(String[] args) {
        Scanner scanner = new Scanner(System.in);
        while (true) {
            System.out.println("1. Leer | 2. Agregar | 3. Filtrar | 4. Ordenar | 5. Salir");
            switch (scanner.nextInt()) {
                case 1 -> leerArchivo();
                case 2 -> agregarLinea(scanner);
                case 3 -> filtrarLineas(scanner);
                case 4 -> ordenarLineas();
                case 5 -> { scanner.close(); return; }
            }
        }
    }
    
    static void leerArchivo() {
        try {
            Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(new File(ARCHIVO));
            doc.getDocumentElement().normalize();
            NodeList lista = doc.getElementsByTagName("persona");
            for (int i = 0; i < lista.getLength(); i++) {
                Element elem = (Element) lista.item(i);
                System.out.println(elem.getElementsByTagName("nombre").item(0).getTextContent() + ", " +
                                   elem.getElementsByTagName("edad").item(0).getTextContent() + " años, " +
                                   elem.getElementsByTagName("departamento").item(0).getTextContent());
            }
        } catch (Exception e) { System.out.println("Error al leer el archivo."); }
    }
    
    static void agregarLinea(Scanner scanner) {
        System.out.println("Ingrese nombre, edad y departamento:");
        scanner.nextLine();
        String[] datos = scanner.nextLine().split(", ");
        try {
            DocumentBuilderFactory factory = DocumentBuilderFactory.newInstance();
            DocumentBuilder builder = factory.newDocumentBuilder();
            Document doc;
            File file = new File(ARCHIVO);
            if (file.exists()) {
                doc = builder.parse(file);
                doc.getDocumentElement().normalize();
            } else {
                doc = builder.newDocument();
                Element root = doc.createElement("personas");
                doc.appendChild(root);
            }
            
            Element persona = doc.createElement("persona");
            Element nombre = doc.createElement("nombre"); nombre.appendChild(doc.createTextNode(datos[0]));
            Element edad = doc.createElement("edad"); edad.appendChild(doc.createTextNode(datos[1]));
            Element departamento = doc.createElement("departamento"); departamento.appendChild(doc.createTextNode(datos[2]));
            persona.appendChild(nombre); persona.appendChild(edad); persona.appendChild(departamento);
            doc.getDocumentElement().appendChild(persona);
            
            Transformer transformer = TransformerFactory.newInstance().newTransformer();
            transformer.setOutputProperty(OutputKeys.INDENT, "yes");
            transformer.transform(new DOMSource(doc), new StreamResult(new File(ARCHIVO)));
        } catch (Exception e) { System.out.println("Error al escribir en el archivo."); }
    }
    
    static void filtrarLineas(Scanner scanner) {
        System.out.println("Ingrese el departamento a filtrar:");
        scanner.nextLine();
        String filtro = scanner.nextLine();
        try {
            Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(new File(ARCHIVO));
            doc.getDocumentElement().normalize();
            NodeList lista = doc.getElementsByTagName("persona");
            for (int i = 0; i < lista.getLength(); i++) {
                Element elem = (Element) lista.item(i);
                if (elem.getElementsByTagName("departamento").item(0).getTextContent().equalsIgnoreCase(filtro)) {
                    System.out.println(elem.getElementsByTagName("nombre").item(0).getTextContent() + ", " +
                                       elem.getElementsByTagName("edad").item(0).getTextContent() + " años, " +
                                       elem.getElementsByTagName("departamento").item(0).getTextContent());
                }
            }
        } catch (Exception e) { System.out.println("Error al filtrar el archivo."); }
    }
    
    static void ordenarLineas() {
        try {
            Document doc = DocumentBuilderFactory.newInstance().newDocumentBuilder().parse(new File(ARCHIVO));
            doc.getDocumentElement().normalize();
            NodeList lista = doc.getElementsByTagName("persona");
            List<Element> personas = new ArrayList<>();
            for (int i = 0; i < lista.getLength(); i++) personas.add((Element) lista.item(i));
            personas.sort(Comparator.comparing(e -> e.getElementsByTagName("nombre").item(0).getTextContent()));
            
            Document nuevoDoc = DocumentBuilderFactory.newInstance().newDocumentBuilder().newDocument();
            Element root = nuevoDoc.createElement("personas");
            nuevoDoc.appendChild(root);
            for (Element persona : personas) {
                Element p = (Element) nuevoDoc.importNode(persona, true);
                root.appendChild(p);
            }
            
            Transformer transformer = TransformerFactory.newInstance().newTransformer();
            transformer.setOutputProperty(OutputKeys.INDENT, "yes");
            transformer.transform(new DOMSource(nuevoDoc), new StreamResult(new File(ARCHIVO)));
        } catch (Exception e) { System.out.println("Error al ordenar el archivo."); }
    }
}
