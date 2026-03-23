# guiaJava

public abstract class UnidadGeografica implements Comparable<UnidadGeografica> {
    protected String nombre;

    public UnidadGeografica(String nombre) {
        this.nombre = nombre;
    }

    public String getNombre() {
        return nombre;
    }

    public abstract double getSuperficie();
    public abstract long getPoblacion();

    // Orden natural: alfabético por nombre
    @Override
    public int compareTo(UnidadGeografica otra) {
        return this.nombre.compareToIgnoreCase(otra.nombre);
    }
}


-------------

public class Pais extends UnidadGeografica {
    private long superficie;
    private double poblacion;

    public Pais(String nombre, long superficie, double poblacion) {
        super(nombre);
        if (superficie <= 0 || poblacion <= 0) {
            throw new IllegalArgumentException("Superficie y población deben ser positivas");
        }
        this.superficie = superficie;
        this.poblacion = poblacion;
    }

    @Override
    public double getSuperficie() {
        return superficie;
    }

    @Override
    public long getPoblacion() {
        return (long) poblacion;
    }

    public void setSuperficie(long superficie) {
        if (superficie <= 0) {
            throw new IllegalArgumentException("Superficie debe ser positiva");
        }
        this.superficie = superficie;
    }

    public void setPoblacion(double poblacion) {
        if (poblacion <= 0) {
            throw new IllegalArgumentException("Población debe ser positiva");
        }
        this.poblacion = poblacion;
    }

    public long getSuperficieValor() {
        return superficie;
    }

    public double getPoblacionValor() {
        return poblacion;
    }

    @Override
    public String toString() {
        return nombre + " - Población: " + poblacion;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Pais)) return false;
        Pais otro = (Pais) obj;
        return this.nombre.equalsIgnoreCase(otro.nombre);
    }
}


---------

import java.util.ArrayList;
import java.util.List;

public class Continente extends UnidadGeografica {
    private List<Pais> paises;

    public Continente(String nombre) {
        super(nombre);
        this.paises = new ArrayList<>();
    }

    public void agregarPais(Pais pais) {
        paises.add(pais);
    }

    public List<Pais> getPaises() {
        return paises;
    }

    @Override
    public double getSuperficie() {
        double total = 0;
        for (Pais p : paises) {
            total += p.getSuperficie();
        }
        return total;
    }

    @Override
    public long getPoblacion() {
        long total = 0;
        for (Pais p : paises) {
            total += p.getPoblacion();
        }
        return total;
    }

    @Override
    public String toString() {
        return nombre;
    }

    @Override
    public boolean equals(Object obj) {
        if (this == obj) return true;
        if (!(obj instanceof Continente)) return false;
        Continente otro = (Continente) obj;
        return this.nombre.equalsIgnoreCase(otro.nombre);
    }
}


---------


import java.util.Comparator;

public class ComparadorPorSuperficie implements Comparator<UnidadGeografica> {
    @Override
    public int compare(UnidadGeografica u1, UnidadGeografica u2) {
        return Double.compare(u1.getSuperficie(), u2.getSuperficie());
    }
}


----------

import java.io.*;
import java.time.LocalDate;
import java.time.format.DateTimeFormatter;
import java.util.*;

public class Main {

    public static void leerDatos(List<Pais> paises, List<Continente> continentes) {
        try (BufferedReader br = new BufferedReader(new FileReader("paises.txt"))) {
            String linea;

            while ((linea = br.readLine()) != null) {
                // Formato ejemplo: Europa;España;505990;47000000
                String[] datos = linea.split(";");

                String nombreContinente = datos[0];
                String nombrePais = datos[1];
                long superficie = Long.parseLong(datos[2]);
                double poblacion = Double.parseDouble(datos[3]);

                Pais pais = new Pais(nombrePais, superficie, poblacion);
                paises.add(pais);

                Continente continente = null;
                for (Continente c : continentes) {
                    if (c.getNombre().equalsIgnoreCase(nombreContinente)) {
                        continente = c;
                        break;
                    }
                }

                if (continente == null) {
                    continente = new Continente(nombreContinente);
                    continentes.add(continente);
                }

                continente.agregarPais(pais);
            }

        } catch (IOException e) {
            System.out.println("Error leyendo archivo: " + e.getMessage());
        }
    }

    public static void main(String[] args) {

        List<Pais> paises = new ArrayList<>();
        List<Continente> continentes = new ArrayList<>();

        leerDatos(paises, continentes);

        // Ordenar continentes por superficie
        continentes.sort(new ComparadorPorSuperficie());

        System.out.println("CONTINENTES ORDENADOS POR SUPERFICIE:");
        for (Continente c : continentes) {
            System.out.println(c.getNombre() + " - Superficie: " + c.getSuperficie());
        }

        // Ordenar países por nombre (orden natural)
        Collections.sort(paises);

        System.out.println("\nPAÍSES ORDENADOS POR NOMBRE:");
        for (Pais p : paises) {
            System.out.println(p.getNombre() + " - Población: " + p.getPoblacion());
        }

        // Fecha actual
        LocalDate hoy = LocalDate.now();
        DateTimeFormatter formato = DateTimeFormatter.ofPattern("dd/MM/yyyy");

        System.out.println("\nFecha: " + hoy.format(formato));
    }
}


----------

