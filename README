package panel;

import java.awt.BorderLayout;
import java.awt.Color;
import java.awt.Dimension;
import java.awt.EventQueue;
import java.awt.Font;
import java.awt.GridBagConstraints;
import java.awt.GridBagLayout;
import java.awt.Insets;
import java.sql.Connection;
import java.sql.DriverManager;
import java.sql.PreparedStatement;
import java.sql.ResultSet;
import java.sql.SQLException;

import javax.swing.BorderFactory;
import javax.swing.JButton;
import javax.swing.JComboBox;
import javax.swing.JFrame;
import javax.swing.JLabel;
import javax.swing.JOptionPane;
import javax.swing.JPanel;
import javax.swing.JScrollPane;
import javax.swing.JTable;
import javax.swing.SwingConstants;
import javax.swing.SwingUtilities;
import javax.swing.Timer;
import javax.swing.UIManager;
import javax.swing.border.EmptyBorder;
import javax.swing.table.DefaultTableModel;
import javax.swing.table.JTableHeader;

public class admin extends JFrame {

    private static final long serialVersionUID = 1L;

    private JComboBox<String> comboUser;
    private JComboBox<String> comboDevice;
    private JComboBox<String> comboStatus;
    private JComboBox<String> comboRole;

    private JTable table;
    private DefaultTableModel tableModel;

    private Timer refreshTimer;

    private java.sql.Timestamp lastKnownUpdate = null;
    private int lastKnownRowCount = -1;

    private static final String DB_URL =
            "jdbc:mysql://ucka.veleri.hr:3306/igrzetic?useSSL=false&serverTimezone=UTC";
    private static final String DB_USER = "igrzetic";
    private static final String DB_PASS = "11";

    private static final String TABLE_NAME   = "chatbot_users";
    private static final String COL_ID       = "id";
    private static final String COL_USERNAME = "username";
    private static final String COL_EMAIL    = "email";
    private static final String COL_ROLE     = "role";
    private static final String COL_DEVICE   = "device_type";
    private static final String COL_STATUS   = "status";
    private static final String COL_CREATED  = "created_at";
    private static final String COL_UPDATED  = "updated_at";

    public static void main(String[] args) {
        EventQueue.invokeLater(() -> {
            try {
                for (UIManager.LookAndFeelInfo info : UIManager.getInstalledLookAndFeels()) {
                    if ("Nimbus".equals(info.getName())) {
                        UIManager.setLookAndFeel(info.getClassName());
                        break;
                    }
                }
            } catch (Exception e) {
            }

            SwingUtilities.invokeLater(() -> {
                admin frame = new admin();
                frame.setVisible(true);
            });
        });
    }

    public admin() {
        setTitle("ChatBot Admin – korisnici");
        setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
        setMinimumSize(new Dimension(900, 500));
        setLocationRelativeTo(null);

        JPanel root = new JPanel(new BorderLayout(10, 10));
        root.setBorder(new EmptyBorder(10, 10, 10, 10));
        setContentPane(root);

        JPanel filterPanel = new JPanel();
        filterPanel.setBorder(BorderFactory.createCompoundBorder(
                BorderFactory.createLineBorder(new Color(220, 220, 220)),
                new EmptyBorder(10, 10, 10, 10)
        ));
        filterPanel.setPreferredSize(new Dimension(230, 0));
        root.add(filterPanel, BorderLayout.WEST);

        GridBagLayout gbl_filterPanel = new GridBagLayout();
        gbl_filterPanel.columnWidths = new int[] {0, 0};
        gbl_filterPanel.rowHeights = new int[] {0, 0, 0, 0, 0, 0};
        gbl_filterPanel.columnWeights = new double[] {0.0, 1.0};
        gbl_filterPanel.rowWeights = new double[] {0.0, 0.0, 0.0, 0.0, 1.0, Double.MIN_VALUE};
        filterPanel.setLayout(gbl_filterPanel);

        JLabel lblFiltersTitle = new JLabel("Filteri");
        lblFiltersTitle.setFont(lblFiltersTitle.getFont().deriveFont(Font.BOLD, 16f));
        GridBagConstraints gbc_lblFiltersTitle = new GridBagConstraints();
        gbc_lblFiltersTitle.gridx = 0;
        gbc_lblFiltersTitle.gridy = 0;
        gbc_lblFiltersTitle.gridwidth = 2;
        gbc_lblFiltersTitle.insets = new Insets(0, 0, 15, 0);
        gbc_lblFiltersTitle.anchor = GridBagConstraints.WEST;
        gbc_lblFiltersTitle.fill = GridBagConstraints.HORIZONTAL;
        filterPanel.add(lblFiltersTitle, gbc_lblFiltersTitle);

        JLabel lblUser = new JLabel("Korisnik");
        GridBagConstraints gbc_lblUser = new GridBagConstraints();
        gbc_lblUser.gridx = 0;
        gbc_lblUser.gridy = 1;
        gbc_lblUser.insets = new Insets(0, 0, 10, 5);
        gbc_lblUser.anchor = GridBagConstraints.WEST;
        filterPanel.add(lblUser, gbc_lblUser);

        comboUser = new JComboBox<>();
        comboUser.setPrototypeDisplayValue("najduzi_username");
        GridBagConstraints gbc_comboUser = new GridBagConstraints();
        gbc_comboUser.gridx = 1;
        gbc_comboUser.gridy = 1;
        gbc_comboUser.insets = new Insets(0, 0, 10, 0);
        gbc_comboUser.fill = GridBagConstraints.HORIZONTAL;
        gbc_comboUser.weightx = 1.0;
        filterPanel.add(comboUser, gbc_comboUser);

        JLabel lblDevice = new JLabel("Uređaj");
        GridBagConstraints gbc_lblDevice = new GridBagConstraints();
        gbc_lblDevice.gridx = 0;
        gbc_lblDevice.gridy = 2;
        gbc_lblDevice.insets = new Insets(0, 0, 10, 5);
        gbc_lblDevice.anchor = GridBagConstraints.WEST;
        filterPanel.add(lblDevice, gbc_lblDevice);

        comboDevice = new JComboBox<>();
        GridBagConstraints gbc_comboDevice = new GridBagConstraints();
        gbc_comboDevice.gridx = 1;
        gbc_comboDevice.gridy = 2;
        gbc_comboDevice.insets = new Insets(0, 0, 10, 0);
        gbc_comboDevice.fill = GridBagConstraints.HORIZONTAL;
        gbc_comboDevice.weightx = 1.0;
        filterPanel.add(comboDevice, gbc_comboDevice);

        JLabel lblStatus = new JLabel("Status");
        GridBagConstraints gbc_lblStatus = new GridBagConstraints();
        gbc_lblStatus.gridx = 0;
        gbc_lblStatus.gridy = 3;
        gbc_lblStatus.insets = new Insets(0, 0, 10, 5);
        gbc_lblStatus.anchor = GridBagConstraints.WEST;
        filterPanel.add(lblStatus, gbc_lblStatus);

        comboStatus = new JComboBox<>();
        GridBagConstraints gbc_comboStatus = new GridBagConstraints();
        gbc_comboStatus.gridx = 1;
        gbc_comboStatus.gridy = 3;
        gbc_comboStatus.insets = new Insets(0, 0, 10, 0);
        gbc_comboStatus.fill = GridBagConstraints.HORIZONTAL;
        gbc_comboStatus.weightx = 1.0;
        filterPanel.add(comboStatus, gbc_comboStatus);

        JLabel lblRole = new JLabel("Uloga");
        GridBagConstraints gbc_lblRole = new GridBagConstraints();
        gbc_lblRole.gridx = 0;
        gbc_lblRole.gridy = 4;
        gbc_lblRole.insets = new Insets(0, 0, 10, 5);
        gbc_lblRole.anchor = GridBagConstraints.WEST;
        filterPanel.add(lblRole, gbc_lblRole);

        comboRole = new JComboBox<>();
        GridBagConstraints gbc_comboRole = new GridBagConstraints();
        gbc_comboRole.gridx = 1;
        gbc_comboRole.gridy = 4;
        gbc_comboRole.insets = new Insets(0, 0, 10, 0);
        gbc_comboRole.fill = GridBagConstraints.HORIZONTAL;
        gbc_comboRole.weightx = 1.0;
        filterPanel.add(comboRole, gbc_comboRole);

        JPanel spacer = new JPanel();
        spacer.setOpaque(false);
        GridBagConstraints gbc_spacer = new GridBagConstraints();
        gbc_spacer.gridx = 0;
        gbc_spacer.gridy = 5;
        gbc_spacer.gridwidth = 2;
        gbc_spacer.weighty = 1.0;
        gbc_spacer.fill = GridBagConstraints.BOTH;
        filterPanel.add(spacer, gbc_spacer);

        JPanel rightPanel = new JPanel(new BorderLayout(10, 10));
        root.add(rightPanel, BorderLayout.CENTER);

        JPanel headerPanel = new JPanel(new BorderLayout());
        headerPanel.setOpaque(false);

        JLabel titleLabel = new JLabel("Pregled korisnika");
        titleLabel.setFont(titleLabel.getFont().deriveFont(Font.BOLD, 18f));
        headerPanel.add(titleLabel, BorderLayout.WEST);

        JLabel subtitleLabel = new JLabel("chatbot_users @ ucka.veleri.hr");
        subtitleLabel.setForeground(new Color(120, 120, 120));
        subtitleLabel.setHorizontalAlignment(SwingConstants.RIGHT);
        headerPanel.add(subtitleLabel, BorderLayout.EAST);

        rightPanel.add(headerPanel, BorderLayout.NORTH);

        tableModel = new DefaultTableModel(
                new Object[]{"ID", "Username", "Email", "Role", "Device", "Status", "Created at"}, 0
        ) {
            @Override
            public boolean isCellEditable(int row, int column) {
                return false;
            }
        };

        table = new JTable(tableModel);
        table.setFillsViewportHeight(true);
        table.setRowHeight(24);
        table.setAutoCreateRowSorter(true);

        table.getColumnModel().getColumn(6).setPreferredWidth(180);
        table.getColumnModel().getColumn(6).setMinWidth(160);

        JTableHeader th = table.getTableHeader();
        th.setReorderingAllowed(false);
        th.setFont(th.getFont().deriveFont(Font.BOLD));
        th.setPreferredSize(new Dimension(th.getPreferredSize().width, 26));

        JScrollPane scrollPane = new JScrollPane(table);
        scrollPane.setBorder(BorderFactory.createLineBorder(new Color(220, 220, 220)));
        rightPanel.add(scrollPane, BorderLayout.CENTER);

        JPanel footerPanel = new JPanel(new BorderLayout());
        footerPanel.setBorder(new EmptyBorder(5, 0, 0, 0));
        rightPanel.add(footerPanel, BorderLayout.SOUTH);

        JPanel bottomPanel = new JPanel(new BorderLayout());
        bottomPanel.setBorder(new EmptyBorder(5, 0, 0, 0));
        root.add(bottomPanel, BorderLayout.SOUTH);

        JButton btnExit = new JButton("Izađi");
        btnExit.setFocusPainted(false);
        bottomPanel.add(btnExit, BorderLayout.WEST);

        JButton btnDelete = new JButton("Izbriši odabranog");
        btnDelete.setFocusPainted(false);
        footerPanel.add(btnDelete, BorderLayout.EAST);

        loadFiltersFromDb();
        loadTableData();
        initDbVersionInfo();

        refreshTimer = new Timer(3000, e -> {
            if (hasDbChanged()) {
                refreshTablePreserveSelection();
            }
        });
        refreshTimer.setInitialDelay(0);
        refreshTimer.start();

        comboUser.addActionListener(e -> loadTableDataAndUpdateDbVersion());
        comboDevice.addActionListener(e -> loadTableDataAndUpdateDbVersion());
        comboStatus.addActionListener(e -> loadTableDataAndUpdateDbVersion());
        comboRole.addActionListener(e -> loadTableDataAndUpdateDbVersion());

        btnDelete.addActionListener(e -> deleteSelectedUser());

        btnExit.addActionListener(e -> {
            int confirm = JOptionPane.showConfirmDialog(
                    this,
                    "Želite li izaći iz aplikacije?",
                    "Izlaz",
                    JOptionPane.YES_NO_OPTION
            );
            if (confirm == JOptionPane.YES_OPTION) {
                if (refreshTimer != null) {
                    refreshTimer.stop();
                }
                dispose();
                System.exit(0);
            }
        });
    }

    private void loadFiltersFromDb() {
        loadDistinctToCombo(comboUser,  COL_USERNAME);
        loadDistinctToCombo(comboDevice, COL_DEVICE);
        loadDistinctToCombo(comboStatus, COL_STATUS);
        loadDistinctToCombo(comboRole,   COL_ROLE);
    }

    private void loadDistinctToCombo(JComboBox<String> combo, String columnName) {
        combo.removeAllItems();
        combo.addItem("Svi");

        String sql = "SELECT DISTINCT " + columnName +
                     " FROM " + TABLE_NAME +
                     " ORDER BY " + columnName;

        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASS);
             PreparedStatement ps = conn.prepareStatement(sql);
             ResultSet rs = ps.executeQuery()) {

            while (rs.next()) {
                String val = rs.getString(1);
                if (val != null && !val.isEmpty()) {
                    combo.addItem(val);
                }
            }
        } catch (SQLException e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(this,
                    "Greška pri učitavanju filtera za stupac: " + columnName,
                    "SQL greška", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void loadTableData() {
        tableModel.setRowCount(0);

        StringBuilder sql = new StringBuilder(
                "SELECT " +
                        COL_ID + ", " +
                        COL_USERNAME + ", " +
                        COL_EMAIL + ", " +
                        COL_ROLE + ", " +
                        COL_DEVICE + ", " +
                        COL_STATUS + ", " +
                        COL_CREATED +
                " FROM " + TABLE_NAME +
                " WHERE 1=1"
        );

        String selUser   = (String) comboUser.getSelectedItem();
        String selDevice = (String) comboDevice.getSelectedItem();
        String selStatus = (String) comboStatus.getSelectedItem();
        String selRole   = (String) comboRole.getSelectedItem();

        if (selUser != null && !"Svi".equals(selUser)) {
            sql.append(" AND ").append(COL_USERNAME).append(" = ?");
        }
        if (selDevice != null && !"Svi".equals(selDevice)) {
            sql.append(" AND ").append(COL_DEVICE).append(" = ?");
        }
        if (selStatus != null && !"Svi".equals(selStatus)) {
            sql.append(" AND ").append(COL_STATUS).append(" = ?");
        }
        if (selRole != null && !"Svi".equals(selRole)) {
            sql.append(" AND ").append(COL_ROLE).append(" = ?");
        }

        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASS);
             PreparedStatement ps = conn.prepareStatement(sql.toString())) {

            int idx = 1;
            if (selUser != null && !"Svi".equals(selUser)) {
                ps.setString(idx++, selUser);
            }
            if (selDevice != null && !"Svi".equals(selDevice)) {
                ps.setString(idx++, selDevice);
            }
            if (selStatus != null && !"Svi".equals(selStatus)) {
                ps.setString(idx++, selStatus);
            }
            if (selRole != null && !"Svi".equals(selRole)) {
                ps.setString(idx++, selRole);
            }

            try (ResultSet rs = ps.executeQuery()) {
                while (rs.next()) {
                    Object[] row = {
                            rs.getInt(COL_ID),
                            rs.getString(COL_USERNAME),
                            rs.getString(COL_EMAIL),
                            rs.getString(COL_ROLE),
                            rs.getString(COL_DEVICE),
                            rs.getString(COL_STATUS),
                            rs.getTimestamp(COL_CREATED)
                    };
                    tableModel.addRow(row);
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(this,
                    "Greška pri učitavanju korisnika iz baze!",
                    "SQL greška", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void refreshTablePreserveSelection() {
        Integer selectedId = null;

        int selectedViewRow = table.getSelectedRow();
        if (selectedViewRow != -1) {
            int selectedModelRow = table.convertRowIndexToModel(selectedViewRow);
            Object val = tableModel.getValueAt(selectedModelRow, 0);
            if (val instanceof Integer) {
                selectedId = (Integer) val;
            }
        }

        loadTableData();

        if (selectedId != null) {
            for (int modelRow = 0; modelRow < tableModel.getRowCount(); modelRow++) {
                Object val = tableModel.getValueAt(modelRow, 0);
                if (val instanceof Integer && ((Integer) val).intValue() == selectedId.intValue()) {
                    int viewRow = table.convertRowIndexToView(modelRow);
                    table.setRowSelectionInterval(viewRow, viewRow);
                    table.scrollRectToVisible(table.getCellRect(viewRow, 0, true));
                    break;
                }
            }
        }
    }

    private void deleteSelectedUser() {
        int row = table.getSelectedRow();

        if (row == -1) {
            JOptionPane.showMessageDialog(this,
                    "Molimo odaberite korisnika za brisanje.",
                    "Nema odabira",
                    JOptionPane.WARNING_MESSAGE);
            return;
        }

        int modelRow = table.convertRowIndexToModel(row);
        int userId = (int) tableModel.getValueAt(modelRow, 0);
        String username = (String) tableModel.getValueAt(modelRow, 1);

        int confirm = JOptionPane.showConfirmDialog(
                this,
                "Želite li sigurno obrisati korisnika: " + username + " (ID: " + userId + ")?",
                "Potvrda brisanja",
                JOptionPane.YES_NO_OPTION
        );

        if (confirm != JOptionPane.YES_OPTION) {
            return;
        }

        String sql = "DELETE FROM " + TABLE_NAME + " WHERE " + COL_ID + " = ?";

        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASS);
             PreparedStatement ps = conn.prepareStatement(sql)) {

            ps.setInt(1, userId);
            ps.executeUpdate();

            JOptionPane.showMessageDialog(this,
                    "Korisnik " + username + " je uspješno obrisan.",
                    "Obrisano",
                    JOptionPane.INFORMATION_MESSAGE);

            refreshTablePreserveSelection();
            initDbVersionInfo();

        } catch (SQLException e) {
            e.printStackTrace();
            JOptionPane.showMessageDialog(this,
                    "Greška pri brisanju korisnika iz baze!",
                    "SQL greška", JOptionPane.ERROR_MESSAGE);
        }
    }

    private void initDbVersionInfo() {
        String sql = "SELECT MAX(" + COL_UPDATED + ") AS max_upd, COUNT(*) AS cnt FROM " + TABLE_NAME;

        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASS);
             PreparedStatement ps = conn.prepareStatement(sql);
             ResultSet rs = ps.executeQuery()) {

            if (rs.next()) {
                lastKnownUpdate  = rs.getTimestamp("max_upd");
                lastKnownRowCount = rs.getInt("cnt");
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
    }

    private boolean hasDbChanged() {
        String sql = "SELECT MAX(" + COL_UPDATED + ") AS max_upd, COUNT(*) AS cnt FROM " + TABLE_NAME;

        try (Connection conn = DriverManager.getConnection(DB_URL, DB_USER, DB_PASS);
             PreparedStatement ps = conn.prepareStatement(sql);
             ResultSet rs = ps.executeQuery()) {

            if (rs.next()) {
                java.sql.Timestamp maxUpd = rs.getTimestamp("max_upd");
                int rowCount = rs.getInt("cnt");

                if (lastKnownUpdate == null && lastKnownRowCount == -1) {
                    lastKnownUpdate  = maxUpd;
                    lastKnownRowCount = rowCount;
                    return true;
                }

                if (rowCount != lastKnownRowCount) {
                    lastKnownRowCount = rowCount;
                    lastKnownUpdate  = maxUpd;
                    return true;
                }

                if (maxUpd != null && lastKnownUpdate != null && maxUpd.after(lastKnownUpdate)) {
                    lastKnownUpdate  = maxUpd;
                    lastKnownRowCount = rowCount;
                    return true;
                }
            }

        } catch (SQLException e) {
            e.printStackTrace();
        }
        return false;
    }

    private void loadTableDataAndUpdateDbVersion() {
        loadTableData();
        initDbVersionInfo();
    }
}
