[package controller;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.time.LocalDate;
import java.util.ArrayList;
import java.util.HashMap;
import java.util.Map;
import java.util.Scanner;
import java.util.logging.Level;
import java.util.logging.Logger;

import model.serviceType;
import model.DetilserviceType;
import model.Customer;
import model.Vendor;
import model.Service;

public class TokoController {
    HashMap<String, serviceType> serviceTypes = new HashMap();
    ArrayList<Service> Services = new ArrayList<>();
    HashMap<String, Customer> Customers = new HashMap();
    HashMap<String, Service> Services = new HashMap();

    public void setUp() {
        serviceType serviceType1 = new serviceType();
        serviceType serviceType2 = new serviceType();
        serviceType serviceType3 = new serviceType();
        serviceType serviceType4 = new serviceType();
        serviceType1
                .setIdserviceType("01")
                .setNamserviceType("Beras 3 Kg")
                .setHargaserviceType(100000)
                .setIsAvailable(true);

        serviceType2.setNamserviceType("Kecap malika 500ml").setIdserviceType("02").setHargaserviceType(50000).setIsAvailable(true);
        serviceType3.setNamserviceType("Saus ABB1 500ml").setIdserviceType("03").setHargaserviceType(25000).setIsAvailable(true);
        serviceType4.setIdserviceType("04").setNamserviceType("tumbler").setHargaserviceType(75000).setIsAvailable(false);

        serviceTypes.put(serviceType1.getIdserviceType(), serviceType1);
        serviceTypes.put(serviceType2.getIdserviceType(), serviceType2);
        serviceTypes.put(serviceType3.getIdserviceType(), serviceType3);
        serviceTypes.put(serviceType4.getIdserviceType(), serviceType4);

        Customer Customer1 = new Customer();
        Customer Customer2 = new Customer();
        Customer1.setIdCustomer("01").setName("Heru").setAlamat("Bandung").setEmail("heru@gmail.com");
        Customer2.setIdCustomer("02").setName("Budi").setAlamat("Jakarta").setEmail("budi@gmail.com");
        Customers.put(Customer1.getIdCustomer(), Customer1);
        Customers.put(Customer2.getIdCustomer(), Customer2);

        Service Service1 = new Service();
        Service Service2 = new Service();
        Service1.setIdService("01").setAlamat("Bandung").setEmail("usop@gmail.com").setName("Usop");
        Service2.setIdService("02").setAlamat("Bandung").setEmail("zoroo@gmail.com").setName("Zoro");
        Services.put(Service1.getIdService(), Service1);
        Services.put(Service2.getIdService(), Service2);
    }

    public void lihatDaftarserviceType() {
        System.out.println();
        System.out.println("==== Daftar serviceType ====");
        for (Map.Entry<String, serviceType> serviceType : serviceTypes.entrySet()) {
            String key = serviceType.getKey();
            serviceType tserviceType = serviceType.getValue();
            if (tserviceType.isIsAvailable()) {
                System.out.println("Kode serviceType \t :" + key);
                System.out.println("Nama \t\t :" + tserviceType.getNamaserviceType());
                System.out.println("Harga \t\t :" + tserviceType.getHargaserviceType());
                System.out.println();
            }

        }
    }

    public serviceType getserviceType(String idserviceType) {
        return serviceTypes.get(idserviceType);
    }

    public Service getService(String idService) {
        return Services.get(idService);
    }

    public Customer getCustomer(String idCustomer) {
        return Customers.get(idCustomer);
    }

    public void belanja() {
        BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
        ArrayList<DetilService> tDetilServices = new ArrayList<>();
        int increment = 0;
        String lanjut = "";
        try {
            do {
                increment++;
                System.out.println();
                System.out.print("Silahkan masukkan kode serviceType\t: ");
                String kodeserviceType = input.readLine();
                System.out.print("Jumlah yang akan dibeli\t: ");
                int jumlah = Integer.valueOf(input.readLine());
                System.out.print("Kembali berbelanja (Y/N)?\t: ");
                lanjut = input.readLine();

                DetilService dp = new DetilService();
                serviceType tserviceType = getserviceType(kodeserviceType);
                dp.setIdDetilService("dp" + increment);
                dp.setserviceType(tserviceType);
                dp.setHargaJual(tserviceType.getHargaserviceType());
                dp.setJumlahBeli(jumlah);
                dp.setSubTotal(tserviceType.getHargaserviceType() * jumlah);
                tDetilServices.add(dp);

            } while (lanjut.equalsIgnoreCase("Y"));

            System.out.println("");
            System.out.println("===Berikut Daftar Belanjaan Anda===");
            tampilkanDaftarBelanjaan(tDetilServices);
            System.out.println("TOTAL: " + hitungTotalBelanja(tDetilServices));
            BufferedReader inputKonfirmasi = new BufferedReader(new InputStreamReader(System.in));
            System.out.println("Konfirmasi Customeran (Y/N)?");
            String konfirm = "n";
            konfirm = inputKonfirmasi.readLine();
            if (konfirm.equalsIgnoreCase("y")) {
                Service Service = new Service();
                Service.setDetilServices(tDetilServices)
                        .setIdService("P" + Services.size() + 1)
                        .setCustomer(getCustomer("01"))
                        .setService(getService("02"))
                        .setTanggal(LocalDate.now())
                        .setTotal(hitungTotalBelanja(tDetilServices));
                Services.add(Service);
            }

        } catch (Exception e) {
            System.out.println(e.getMessage());
        }
    }

    public void tampilkanDaftarBelanjaan(ArrayList<DetilService> dp) {
        for (DetilService detilService : dp) {
            System.out.println("Nama serviceType \t :" + detilService.getserviceType().getNamaserviceType());
            System.out.println("Harga \t :" + detilService.getHargaJual());
            System.out.println("Jumlah \t :" + detilService.getJumlahBeli());
            System.out.println("");
        }
    }

    public int hitungTotalBelanja(ArrayList<DetilService> dp) {
        int total = 0;
        for (DetilService detilService : dp) {
            total += detilService.getSubTotal();
        }
        return total;
    }

    public void tampilkanLaporanService() {
        for (Service Service : Services) {
            System.err.println();
            System.out.println("Kode Service \t :"+Service.getIdService());
            System.out.println("Tanggal transaksi \t :"+Service.getTanggal());
            System.out.println("Service \t \t : "+Service.getService().getName());
            System.out.println("Customer \t \t : "+Service.getCustomer().getName());
            System.out.println("=========================");
            ArrayList<DetilService> detilServices = Service.getDetilServices();
            for (DetilService dp : detilServices) {
                System.out.println("Nama serviceType \t \t : " + dp.getserviceType().getNamaserviceType());
                System.out.println("Jumlah beli \t \t : " + dp.getJumlahBeli());
                System.out.println("Subtotal \t \t : " + dp.getSubTotal());
            }
            System.out.println("=========================");
            System.out.println("Total \t \t : "+Service.getTotal());
        }
    }

}
](http://127.0.0.1:5500/uas.html)http://127.0.0.1:5500/uas.html
