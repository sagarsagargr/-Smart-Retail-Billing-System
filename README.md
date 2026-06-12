# -Smart-Retail-Billing-System
Smart Retail Billing Syste
[6/12, 11:44 AM] Sagar: import javax.swing.*;
import javax.swing.border.EmptyBorder; import javax.swing.event.ListSelectionEvent; import javax.swing.table.DefaultTableModel; import java.awt.*;
import java.awt.event.*; import java.util.ArrayList;

class Expense { String date; String type; double amount;

public Expense(String date, String type, double amount) { this.date = date;
this.type = type; this.amount = amount;
}
}

class Feedback { String text;
int rating;

public Feedback(String text, int rating) { this.text = text;
this.rating = rating;
}
}

class ExpenseManagerGUI {
private final java.util.List<Expense> records = new ArrayList<>();
private final java.util.List<Feedback> feedbacks = new ArrayList<>();
[6/12, 11:44 AM] Sagar: public void addExpense(Expense e) { records.add(e);
}
public void updateExpense(int index, Expense e) { if (index >= 0 && index < records.size()) { records.set(index, e);
}
}
public void deleteExpense(int index) {
if (index >= 0 && index < records.size()) { records.remove(index);
}
}

public java.util.List<Expense> getExpenses() { return records;
}

public double getTotal() { double total = 0.0;
for (Expense e : records) total += e.amount; return total;
}

public void addFeedback(Feedback f) { feedbacks.add(f);
}

public java.util.List<Feedback> getFeedbacks() { return feedbacks;
}
}

public class ExpenseTrackerGUI extends JFrame { private final ExpenseManagerGUI manager = new ExpenseManagerGUI();

private final DefaultTableModel tableModel = new DefaultTableModel(
new Object[]{"No.", "Date", "Type", "Amount (₹)"}, 0) {
[6/12, 11:45 AM] Sagar: @Override
public boolean isCellEditable(int row, int column) { return false;
}
};

private final JTable table = new JTable(tableModel); private final JLabel totalLabel = new JLabel("Total:
₹0.00");
private final JButton btnEdit = new JButton("Edit"); private final JButton btnDelete = new JButton("Delete");

public ExpenseTrackerGUI() { setTitle("Expense Tracker");
setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE); setSize(650, 450);
setLocationRelativeTo(null); createUI(); attachListeners();
}

private void createUI() {
JPanel root = new JPanel(new BorderLayout(10, 10)); root.setBorder(new EmptyBorder(10, 10, 10, 10)); setContentPane(root);

JLabel heading = new JLabel("Expense Tracker"); heading.setFont(heading.getFont().deriveFont(Font.BOLD, 20f));
root.add(heading, BorderLayout.NORTH);

table.setSelectionMode(ListSelectionModel.SINGLE_SELECTION)
;
table.setFillsViewportHeight(true); JScrollPane sp = new JScrollPane(table); root.add(sp, BorderLayout.CENTER);

JPanel right = new JPanel();
right.setLayout(new BoxLayout(right, BoxLayout.Y_AXIS)); right.setBorder(new EmptyBorder(0, 10, 0, 0));
[6/12, 11:45 AM] Sagar: JButton btnAdd = new JButton("Add Expense");

btnAdd.setAlignmentX(Component.CENTER_ALIGNMENT); btnEdit.setAlignmentX(Component.CENTER_ALIGNMENT); btnDelete.setAlignmentX(Component.CENTER_ALIGNMENT); JButton btnTotal = new JButton("View Total"); btnTotal.setAlignmentX(Component.CENTER_ALIGNMENT); JButton btnFeedback = new JButton("Give Feedback"); btnFeedback.setAlignmentX(Component.CENTER_ALIGNMENT); JButton btnExit = new JButton("Exit"); btnExit.setAlignmentX(Component.CENTER_ALIGNMENT);

right.add(btnAdd); right.add(Box.createVerticalStrut(8)); right.add(btnEdit); right.add(Box.createVerticalStrut(8)); right.add(btnDelete); right.add(Box.createVerticalStrut(20)); right.add(btnTotal); right.add(Box.createVerticalStrut(8)); right.add(totalLabel); right.add(Box.createVerticalStrut(20)); right.add(btnFeedback); right.add(Box.createVerticalStrut(20)); right.add(btnExit);

root.add(right, BorderLayout.EAST);

JLabel hint = new JLabel("Select a row to Edit / Delete. Add expense to get started.");
root.add(hint, BorderLayout.SOUTH);
btnAdd.addActionListener(e -> openExpenseDialog(null, -1)); btnEdit.addActionListener(e -> {
int sel = table.getSelectedRow(); if (sel == -1) {
JOptionPane.showMessageDialog(this, "Select a row to edit.", "No selection", JOptionPane.WARNING_MESSAGE); return;
}
[6/12, 11:46 AM] Sagar: int modelIndex = table.convertRowIndexToModel(sel);

openExpenseDialog(manager.getExpenses().get(modelIndex), modelIndex);
});

btnDelete.addActionListener(e -> { int sel = table.getSelectedRow(); if (sel == -1) {
JOptionPane.showMessageDialog(this, "Select a row to delete.", "No selection", JOptionPane.WARNING_MESSAGE); return;
}
int modelIndex = table.convertRowIndexToModel(sel);
int confirm = JOptionPane.showConfirmDialog(this, "Delete selected expense?", "Confirm delete", JOptionPane.YES_NO_OPTION);
if (confirm == JOptionPane.YES_OPTION) { manager.deleteExpense(modelIndex); refreshTable();
}
});

btnTotal.addActionListener(e -> updateTotalLabel()); btnFeedback.addActionListener(e -> openFeedbackDialog()); btnExit.addActionListener(e -> {
int c = JOptionPane.showConfirmDialog(this, "Exit application?", "Exit", JOptionPane.YES_NO_OPTION); if (c == JOptionPane.YES_OPTION) System.exit(0);
});

btnEdit.setEnabled(false); btnDelete.setEnabled(false);

table.getSelectionModel().addListSelectionListener((ListSel ectionEvent e) -> {
boolean selected = table.getSelectedRow() != -1; btnEdit.setEnabled(selected); btnDelete.setEnabled(selected);
});
}
[6/12, 11:46 AM] Sagar: private void attachListeners() {
}

private void openExpenseDialog(Expense existing, int indexToUpdate)

{
JDialog dialog = new JDialog(this, existing==null ? "Add Expense" : "Edit Expense", true);
dialog.setDefaultCloseOperation(JDialog.DISPOSE_ON_CLOSE); dialog.setSize(380, 260); dialog.setLocationRelativeTo(this);

JPanel p = new JPanel(new GridBagLayout()); p.setBorder(new EmptyBorder(10, 10, 10, 10)); GridBagConstraints gbc = new GridBagConstraints(); gbc.insets = new Insets(6, 6, 6, 6);
gbc.fill = GridBagConstraints.HORIZONTAL;

JLabel lblDate = new JLabel("Date (dd/mm/yyyy):"); JTextField txtDate = new JTextField();
JLabel lblType = new JLabel("Type:"); JTextField txtType = new JTextField(); JLabel lblAmount = new JLabel("Amount (₹):"); JTextField txtAmount = new JTextField();

if (existing != null) { txtDate.setText(existing.date); txtType.setText(existing.type); txtAmount.setText(String.valueOf(existing.amount));
}

gbc.gridx = 0; gbc.gridy = 0; p.add(lblDate, gbc); gbc.gridx = 1; gbc.gridy = 0; p.add(txtDate, gbc); gbc.gridx = 0; gbc.gridy = 1; p.add(lblType, gbc); gbc.gridx = 1; gbc.gridy = 1; p.add(txtType, gbc); gbc.gridx = 0; gbc.gridy = 2; p.add(lblAmount, gbc); gbc.gridx = 1; gbc.gridy = 2; p.add(txtAmount, gbc);

JButton btnSave = new JButton(existing == null ? "Add" : "Update");
[6/12, 11:46 AM] Sagar: JButton btnCancel = new JButton("Cancel"); JPanel btnPanel = new JPanel(new FlowLayout(FlowLayout.RIGHT)); btnPanel.add(btnSave); btnPanel.add(btnCancel);
gbc.gridx = 0; gbc.gridy = 3; gbc.gridwidth = 2; p.add(btnPanel, gbc);
dialog.setContentPane(p); btnCancel.addActionListener(e -> dialog.dispose());

btnSave.addActionListener(e -> { String date = txtDate.getText().trim(); String type = txtType.getText().trim();
String amtStr = txtAmount.getText().trim();

if(date.isEmpty() || type.isEmpty() || amtStr.isEmpty())
{
JOptionPane.showMessageDialog(dialog, "All fields are required.", "Validation", JOptionPane.WARNING_MESSAGE); return;
}

double amount; try {
amount = Double.parseDouble(amtStr);
} catch (NumberFormatException nfe) { JOptionPane.showMessageDialog(dialog, "Enter a valid numeric amount.", "Validation", JOptionPane.WARNING_MESSAGE);
return;
}

if (amount < 0) { JOptionPane.showMessageDialog(dialog, "Amount cannot be negative.", "Validation", JOptionPane.WARNING_MESSAGE); return;
}

if (existing == null) {
manager.addExpense(new Expense(date, type, amount));
} else {
[6/12, 11:46 AM] Sagar: manager.updateExpense(indexToUpdate, new Expense(date, type, amount));
}
refreshTable(); dialog.dispose();
});
dialog.setVisible(true);
}
private void openFeedbackDialog() {
JDialog dialog = new JDialog(this, "Give Feedback", true); dialog.setSize(420, 300); dialog.setLocationRelativeTo(this);
JPanel p = new JPanel(new BorderLayout(8, 8)); p.setBorder(new EmptyBorder(10, 10, 10, 10));

JTextArea txtFeedback = new JTextArea(6, 30); txtFeedback.setLineWrap(true); txtFeedback.setWrapStyleWord(true);
JPanel top = new JPanel(new BorderLayout(6, 6)); top.add(new JLabel("Feedback (comments):"), BorderLayout.NORTH);
top.add(new JScrollPane(txtFeedback), BorderLayout.CENTER);

JPanel bottom = new JPanel(new FlowLayout(FlowLayout.RIGHT));
JLabel lblRating = new JLabel("Rating:"); Integer[] ratings = {1, 2, 3, 4, 5};
JComboBox<Integer> cmbRating = new JComboBox<>(ratings); cmbRating.setSelectedIndex(4);
JButton btnSubmit = new JButton("Submit"); JButton btnCancel = new JButton("Cancel");

bottom.add(lblRating); bottom.add(cmbRating); bottom.add(btnSubmit); bottom.add(btnCancel); p.add(top, BorderLayout.CENTER);
p.add(bottom, BorderLayout.SOUTH); dialog.setContentPane(p); btnCancel.addActionListener(e -> dialog.dispose());
[6/12, 11:47 AM] Sagar: btnSubmit.addActionListener(e -> {
String text = txtFeedback.getText().trim(); if (text.isEmpty()) {
JOptionPane.showMessageDialog(dialog, "Please enter feedback text.", "Validation", JOptionPane.WARNING_MESSAGE);
return;
}
int rating = (Integer) cmbRating.getSelectedItem(); manager.addFeedback(new Feedback(text, rating)); JOptionPane.showMessageDialog(dialog, "Thank you for your feedback!", "Recorded", JOptionPane.INFORMATION_MESSAGE); dialog.dispose();
});
dialog.setVisible(true);
}
private void refreshTable() { tableModel.setRowCount(0);
java.util.List<Expense> list = manager.getExpenses(); for (int i = 0; i < list.size(); i++) {
Expense e = list.get(i);
tableModel.addRow(new Object[]{i + 1, e.date, e.type, String.format("%.2f", e.amount)});
}
updateTotalLabel();
}
private void updateTotalLabel() { double total = manager.getTotal();
totalLabel.setText(String.format("Total: ₹%.2f", total));
}
public static void main(String[] args) { SwingUtilities.invokeLater(() -> {
try { UIManager.setLookAndFeel(UIManager.getSystemLookAndFeel ClassName());
} catch (Exception ignored) {}
ExpenseTrackerGUI gui = new ExpenseTrackerGUI(); gui.setVisible(true);
});
}
}
