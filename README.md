package pharmaceutical;

import javax.swing.*;
import javax.swing.table.DefaultTableModel;
import java.awt.*;
import java.util.ArrayList;
import java.util.Comparator;
import java.util.List;

class Medicine {
    String name;
    String manufacturer;
    float price;
    int quantity;

    Medicine(String name, String manufacturer, float price, int quantity) {
        this.name = name;
        this.manufacturer = manufacturer;
        this.price = price;
        this.quantity = quantity;
    }
}

public class PharmaceuticalManagementSystem {

    private List<Medicine> medicines = new ArrayList<>();
    private JFrame frame;
    private JTable table;
    private DefaultTableModel tableModel;

    public PharmaceuticalManagementSystem() {
        initializeGUI();
        addDefaultMedicines();
        updateTable();
    }

    private void initializeGUI() {
        frame = new JFrame("Pharmaceutical Management System");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(800, 600);

        // Table
        tableModel = new DefaultTableModel(new String[]{"Name", "Manufacturer", "Price", "Quantity"}, 0);
        table = new JTable(tableModel);
        JScrollPane scrollPane = new JScrollPane(table);

        // Buttons
        JButton addButton = new JButton("Add Medicine");
        JButton updateButton = new JButton("Update Medicine");
        JButton checkStockButton = new JButton("Check Stock");
        JButton sortByNameButton = new JButton("Sort by Name");
        JButton sortByPriceButton = new JButton("Sort by Price (Merge Sort)");
        JButton selectionSortButton = new JButton("Sort by Manufacturer (Selection Sort)");
        JButton quickSortButton = new JButton("Sort by Quantity (Quick Sort)");

        // Button Actions
        addButton.addActionListener(e -> addMedicine());
        updateButton.addActionListener(e -> updateMedicine());
        checkStockButton.addActionListener(e -> checkStock());
        sortByNameButton.addActionListener(e -> {
            sortMedicinesByName();
            updateTable();
        });
        sortByPriceButton.addActionListener(e -> {
            sortMedicinesByPrice();
            updateTable();
        });
        selectionSortButton.addActionListener(e -> {
            selectionSortByManufacturer();
            updateTable();
        });
        quickSortButton.addActionListener(e -> {
            quickSortByQuantity(0, medicines.size() - 1);
            updateTable();
        });

        JPanel buttonPanel = new JPanel();
        buttonPanel.add(addButton);
        buttonPanel.add(updateButton);
        buttonPanel.add(checkStockButton);
        buttonPanel.add(sortByNameButton);
        buttonPanel.add(sortByPriceButton);
        buttonPanel.add(selectionSortButton);
        buttonPanel.add(quickSortButton);

        frame.add(scrollPane, BorderLayout.CENTER);
        frame.add(buttonPanel, BorderLayout.SOUTH);
        frame.setVisible(true);
    }

    private void addDefaultMedicines() {
        medicines.add(new Medicine("Paracetamol", "XYZ Pharmaceuticals", 10.5f, 100));
        medicines.add(new Medicine("Ibuprofen", "ABC Pharma", 8.75f, 50));
        medicines.add(new Medicine("Aspirin", "XYZ Pharmaceuticals", 7.25f, 75));
    }

    private void addMedicine() {
        String name = JOptionPane.showInputDialog(frame, "Enter medicine name:");
        String manufacturer = JOptionPane.showInputDialog(frame, "Enter manufacturer:");
        String priceStr = JOptionPane.showInputDialog(frame, "Enter price:");
        String quantityStr = JOptionPane.showInputDialog(frame, "Enter quantity:");

        try {
            float price = Float.parseFloat(priceStr);
            int quantity = Integer.parseInt(quantityStr);
            medicines.add(new Medicine(name, manufacturer, price, quantity));
            updateTable();
            JOptionPane.showMessageDialog(frame, "Medicine added successfully.");
        } catch (NumberFormatException e) {
            JOptionPane.showMessageDialog(frame, "Invalid input for price or quantity.");
        }
    }

    private void updateMedicine() {
        String name = JOptionPane.showInputDialog(frame, "Enter the name of the medicine to update:");
        for (Medicine med : medicines) {
            if (med.name.equalsIgnoreCase(name)) {
                String quantityStr = JOptionPane.showInputDialog(frame, "Enter the new quantity:");
                try {
                    med.quantity = Integer.parseInt(quantityStr);
                    updateTable();
                    JOptionPane.showMessageDialog(frame, "Medicine updated successfully.");
                    return;
                } catch (NumberFormatException e) {
                    JOptionPane.showMessageDialog(frame, "Invalid input for quantity.");
                    return;
                }
            }
        }
        JOptionPane.showMessageDialog(frame, "Medicine not found.");
    }

    private void checkStock() {
        String name = JOptionPane.showInputDialog(frame, "Enter the name of the medicine to check stock:");
        for (Medicine med : medicines) {
            if (med.name.equalsIgnoreCase(name)) {
                JOptionPane.showMessageDialog(frame, "Stock for " + med.name + ": " + med.quantity);
                return;
            }
        }
        JOptionPane.showMessageDialog(frame, "Medicine not found.");
    }

    private void sortMedicinesByName() {
        medicines.sort(Comparator.comparing(med -> med.name));
    }

    private void sortMedicinesByPrice() {
        mergeSort(medicines, 0, medicines.size() - 1, Comparator.comparingDouble(med -> med.price));
    }

    private void mergeSort(List<Medicine> list, int left, int right, Comparator<Medicine> comparator) {
        if (left < right) {
            int mid = left + (right - left) / 2;
            mergeSort(list, left, mid, comparator);
            mergeSort(list, mid + 1, right, comparator);
            merge(list, left, mid, right, comparator);
        }
    }

    private void merge(List<Medicine> list, int left, int mid, int right, Comparator<Medicine> comparator) {
        List<Medicine> leftList = new ArrayList<>(list.subList(left, mid + 1));
        List<Medicine> rightList = new ArrayList<>(list.subList(mid + 1, right + 1));

        int i = 0, j = 0, k = left;
        while (i < leftList.size() && j < rightList.size()) {
            if (comparator.compare(leftList.get(i), rightList.get(j)) <= 0) {
                list.set(k++, leftList.get(i++));
            } else {
                list.set(k++, rightList.get(j++));
            }
        }

        while (i < leftList.size()) {
            list.set(k++, leftList.get(i++));
        }

        while (j < rightList.size()) {
            list.set(k++, rightList.get(j++));
        }
    }

    private void selectionSortByManufacturer() {
        int n = medicines.size();
        for (int i = 0; i < n - 1; i++) {
            int minIndex = i;
            for (int j = i + 1; j < n; j++) {
                if (medicines.get(j).manufacturer.compareTo(medicines.get(minIndex).manufacturer) < 0) {
                    minIndex = j;
                }
            }
            Medicine temp = medicines.get(minIndex);
            medicines.set(minIndex, medicines.get(i));
            medicines.set(i, temp);
        }
    }

    private void quickSortByQuantity(int low, int high) {
        if (low < high) {
            int pi = partition(low, high);
            quickSortByQuantity(low, pi - 1);
            quickSortByQuantity(pi + 1, high);
        }
    }

    private int partition(int low, int high) {
        int pivot = medicines.get(high).quantity;
        int i = (low - 1);
        for (int j = low; j < high; j++) {
            if (medicines.get(j).quantity <= pivot) {
                i++;
                Medicine temp = medicines.get(i);
                medicines.set(i, medicines.get(j));
                medicines.set(j, temp);
            }
        }
        Medicine temp = medicines.get(i + 1);
        medicines.set(i + 1, medicines.get(high));
        medicines.set(high, temp);
        return i + 1;
    }

    private void updateTable() {
        tableModel.setRowCount(0);
        for (Medicine med : medicines) {
            tableModel.addRow(new Object[]{med.name, med.manufacturer, med.price, med.quantity});
        }
    }

    public static void main(String[] args) {
        SwingUtilities.invokeLater(PharmaceuticalManagementSystem::new);
    }
}
