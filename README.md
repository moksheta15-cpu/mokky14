// index.java — Tapback simple offline test app
// Requirements: Java 8+ and ZXing library (core)

import com.google.zxing.BarcodeFormat;
import com.google.zxing.WriterException;
import com.google.zxing.qrcode.QRCodeWriter;
import com.google.zxing.common.BitMatrix;

import javax.swing.*;
import java.awt.*;
import java.awt.image.BufferedImage;
import java.io.File;
import javax.imageio.ImageIO;

public class index {

    private JTextField nameField;
    private JTextField phoneField;
    private JLabel qrLabel;

    public static void main(String[] args) {
        SwingUtilities.invokeLater(index::new);
    }

    public index() {
        JFrame frame = new JFrame("Tapback – Registration (Java)");
        frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        frame.setSize(500, 450);

        JPanel formPanel = new JPanel(new GridLayout(5, 1, 10, 10));

        nameField = new JTextField();
        phoneField = new JTextField();

        JButton generateButton = new JButton("Generate QR");
        qrLabel = new JLabel("QR will appear here", SwingConstants.CENTER);

        generateButton.addActionListener(e -> generateQR());

        formPanel.add(new JLabel("Full Name:"));
        formPanel.add(nameField);
        formPanel.add(new JLabel("Phone:"));
        formPanel.add(phoneField);
        formPanel.add(generateButton);

        frame.add(formPanel, BorderLayout.NORTH);
        frame.add(qrLabel, BorderLayout.CENTER);

        frame.setVisible(true);
    }

    private void generateQR() {
        String name = nameField.getText().trim();
        String phone = phoneField.getText().trim();

        if (name.isEmpty() || phone.isEmpty()) {
            JOptionPane.showMessageDialog(null, "Please enter both name and phone.");
            return;
        }

        // Simple JSON-like payload
        String payload = "{ \"id\":\"" + System.currentTimeMillis() +
                "\", \"name\":\"" + name +
                "\", \"phone\":\"" + phone + "\" }";

        try {
            int size = 250;
            QRCodeWriter qrCodeWriter = new QRCodeWriter();
            BitMatrix bitMatrix = qrCodeWriter.encode(payload, BarcodeFormat.QR_CODE, size, size);

            BufferedImage qrImage = new BufferedImage(size, size, BufferedImage.TYPE_INT_RGB);
            for (int x = 0; x < size; x++) {
                for (int y = 0; y < size; y++) {
                    qrImage.setRGB(x, y, bitMatrix.get(x, y) ? Color.BLACK.getRGB() : Color.WHITE.getRGB());
                }
            }

            // Show QR in GUI
            qrLabel.setIcon(new ImageIcon(qrImage));

            // Save as PNG
            ImageIO.write(qrImage, "png", new File("tapback-qr.png"));
            JOptionPane.showMessageDialog(null, "QR saved as tapback-qr.png");

        } catch (WriterException | java.io.IOException ex) {
            ex.printStackTrace();
        }
    }
}
