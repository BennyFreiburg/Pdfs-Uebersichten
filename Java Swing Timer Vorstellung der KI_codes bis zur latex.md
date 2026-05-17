import javax.swing.*;

public class HelloSwing {
 public static void main(String[] args) {
 JFrame frame = new JFrame("Hallo Swing");
 JButton button = new JButton("Klick mich");

 button.addActionListener(e -> JOptionPane.showMessageDialog(frame, "Hallo!"));

 frame.add(button);
 frame.setSize(300, 200);
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setVisible(true);
 }
}

--------

import javax.swing.*;
import java.awt.*;

public class TimerBeispiel {
 public static void main(String[] args) {
 JFrame frame = new JFrame("Timer");
 JLabel label = new JLabel("0", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 40));

 frame.add(label);
 frame.setSize(300, 200);
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setVisible(true);

 final int[] sekunden = {0};
 Timer timer = new Timer(1000, e -> {
 sekunden[0]++;
 label.setText(String.valueOf(sekunden[0]));
 });
 timer.start();
 }
}

------

import javax.swing.*;
import java.awt.*;

public class CountdownTimer {
 public static void main(String[] args) {
 JFrame frame = new JFrame("Countdown");
 JLabel label = new JLabel("10", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 48));

 JButton startButton = new JButton("Start");

 JPanel panel = new JPanel(new BorderLayout());
 panel.add(label, BorderLayout.CENTER);
 panel.add(startButton, BorderLayout.SOUTH);

 frame.setContentPane(panel);
 frame.setSize(300, 200);
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setLocationRelativeTo(null);
 frame.setVisible(true);

 final int[] seconds = {10};
 Timer timer = new Timer(1000, e -> {
 if (seconds[0] > 0) {
 seconds[0]--;
 label.setText(String.valueOf(seconds[0]));
 } else {
 ((Timer) e.getSource()).stop();
 label.setText("Fertig!");
 }
 });

 startButton.addActionListener(e -> {
 seconds[0] = 10;
 label.setText(String.valueOf(seconds[0]));
 timer.start();
 });
 }
}


---------

import javax.swing.*;
import java.awt.*;

public class CountdownMitEingabe {
 public static void main(String[] args) {
 JFrame frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(350, 200);
 frame.setLocationRelativeTo(null);

 JLabel label = new JLabel("Sekunden eingeben und starten", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 20));

 JTextField inputField = new JTextField("10", 10);
 JButton startButton = new JButton("Start");

 JPanel topPanel = new JPanel();
 topPanel.add(new JLabel("Sekunden:"));
 topPanel.add(inputField);
 topPanel.add(startButton);

 frame.setLayout(new BorderLayout());
 frame.add(label, BorderLayout.CENTER);
 frame.add(topPanel, BorderLayout.SOUTH);
 frame.setVisible(true);

 final Timer[] timer = new Timer[1];

 startButton.addActionListener(e -> {
 if (timer[0] != null && timer[0].isRunning()) {
 timer[0].stop();
 }

 int seconds;
 try {
 seconds = Integer.parseInt(inputField.getText().trim());
 if (seconds < 0) {
 label.setText("Bitte eine positive Zahl eingeben");
 return;
 }
 } catch (NumberFormatException ex) {
 label.setText("Ungültige Eingabe");
 return;
 }

 label.setText(String.valueOf(seconds));

 final int[] remaining = {seconds};
 timer[0] = new Timer(1000, ev -> {
 remaining[0]--;
 if (remaining[0] >= 0) {
 label.setText(String.valueOf(remaining[0]));
 }
 if (remaining[0] <= 0) {
 ((Timer) ev.getSource()).stop();
 label.setText("Fertig!");
 }
 });
 timer[0].start();
 });
 }
}


----------


import javax.swing.*;
import java.awt.*;

public class CountdownMitSteuerung {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownMitSteuerung().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel label;
 private JTextField inputField;
 private JButton startButton;
 private JButton stopButton;
 private JButton resetButton;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(400, 220);
 frame.setLocationRelativeTo(null);
 frame.setLayout(new BorderLayout(10, 10));

 label = new JLabel("Sekunden eingeben", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 28));

 inputField = new JTextField("10", 8);
 startButton = new JButton("Start");
 stopButton = new JButton("Stop");
 resetButton = new JButton("Reset");

 stopButton.setEnabled(false);
 resetButton.setEnabled(false);

 JPanel inputPanel = new JPanel();
 inputPanel.add(new JLabel("Sekunden:"));
 inputPanel.add(inputField);

 JPanel buttonPanel = new JPanel();
 buttonPanel.add(startButton);
 buttonPanel.add(stopButton);
 buttonPanel.add(resetButton);

 frame.add(label, BorderLayout.CENTER);
 frame.add(inputPanel, BorderLayout.NORTH);
 frame.add(buttonPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 if (remainingSeconds >= 0) {
 label.setText(String.valueOf(remainingSeconds));
 }
 if (remainingSeconds <= 0) {
 timer.stop();
 label.setText("Fertig!");
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(true);
 }
 });

 startButton.addActionListener(e -> {
 if (!timer.isRunning()) {
 if (remainingSeconds <= 0) {
 try {
 initialSeconds = Integer.parseInt(inputField.getText().trim());
 if (initialSeconds < 0) {
 label.setText("Bitte positive Zahl");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 label.setText("Ungültige Eingabe");
 return;
 }
 }

 label.setText(String.valueOf(remainingSeconds));
 timer.start();
 startButton.setEnabled(false);
 stopButton.setEnabled(true);
 resetButton.setEnabled(true);
 inputField.setEnabled(false);
 }
 });

 stopButton.addActionListener(e -> {
 if (timer.isRunning()) {
 timer.stop();
 label.setText("Pausiert bei: " + remainingSeconds);
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 remainingSeconds = initialSeconds;
 label.setText("Sekunden eingeben");
 inputField.setEnabled(true);
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }
}


-------

import javax.swing.*;
import java.awt.*;

public class CountdownMitMinutenSekunden {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownMitMinutenSekunden().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel label;
 private JTextField minutesField;
 private JTextField secondsField;
 private JButton startButton;
 private JButton stopButton;
 private JButton resetButton;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(450, 240);
 frame.setLocationRelativeTo(null);
 frame.setLayout(new BorderLayout(10, 10));

 label = new JLabel("00:00", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 40));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 startButton = new JButton("Start");
 stopButton = new JButton("Stop");
 resetButton = new JButton("Reset");

 stopButton.setEnabled(false);
 resetButton.setEnabled(false);

 JPanel inputPanel = new JPanel();
 inputPanel.add(new JLabel("Minuten:"));
 inputPanel.add(minutesField);
 inputPanel.add(new JLabel("Sekunden:"));
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel();
 buttonPanel.add(startButton);
 buttonPanel.add(stopButton);
 buttonPanel.add(resetButton);

 frame.add(label, BorderLayout.CENTER);
 frame.add(inputPanel, BorderLayout.NORTH);
 frame.add(buttonPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateLabel();

 if (remainingSeconds <= 0) {
 timer.stop();
 label.setText("Fertig!");
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(true);
 }
 });

 startButton.addActionListener(e -> {
 if (!timer.isRunning()) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 label.setText("Ungültige Eingabe");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 label.setText("Ungültige Eingabe");
 return;
 }
 }

 updateLabel();
 timer.start();
 startButton.setEnabled(false);
 stopButton.setEnabled(true);
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 }
 });

 stopButton.addActionListener(e -> {
 if (timer.isRunning()) {
 timer.stop();
 updateLabel();
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 remainingSeconds = initialSeconds;
 updateLabel();
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateLabel() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 label.setText(String.format("%02d:%02d", minutes, seconds));
 }
}


-----


import javax.swing.*;
import java.awt.*;

public class CountdownMitSignal {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownMitSignal().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel label;
 private JTextField minutesField;
 private JTextField secondsField;
 private JButton startButton;
 private JButton stopButton;
 private JButton resetButton;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(450, 240);
 frame.setLocationRelativeTo(null);
 frame.setLayout(new BorderLayout(10, 10));

 label = new JLabel("00:00", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 40));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 startButton = new JButton("Start");
 stopButton = new JButton("Stop");
 resetButton = new JButton("Reset");

 stopButton.setEnabled(false);
 resetButton.setEnabled(false);

 JPanel inputPanel = new JPanel();
 inputPanel.add(new JLabel("Minuten:"));
 inputPanel.add(minutesField);
 inputPanel.add(new JLabel("Sekunden:"));
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel();
 buttonPanel.add(startButton);
 buttonPanel.add(stopButton);
 buttonPanel.add(resetButton);

 frame.add(label, BorderLayout.CENTER);
 frame.add(inputPanel, BorderLayout.NORTH);
 frame.add(buttonPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateLabel();

 if (remainingSeconds <= 0) {
 timer.stop();
 Toolkit.getDefaultToolkit().beep();
 label.setText("Fertig!");
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(true);
 }
 });

 startButton.addActionListener(e -> {
 if (!timer.isRunning()) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 label.setText("Ungültige Eingabe");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 label.setText("Ungültige Eingabe");
 return;
 }
 }

 updateLabel();
 timer.start();
 startButton.setEnabled(false);
 stopButton.setEnabled(true);
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 }
 });

 stopButton.addActionListener(e -> {
 if (timer.isRunning()) {
 timer.stop();
 updateLabel();
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 remainingSeconds = initialSeconds;
 updateLabel();
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateLabel() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 label.setText(String.format("%02d:%02d", minutes, seconds));
 }
}

-------


import javax.swing.*;
import java.awt.*;

public class CountdownMitFortschritt {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownMitFortschritt().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel label;
 private JTextField minutesField;
 private JTextField secondsField;
 private JButton startButton;
 private JButton stopButton;
 private JButton resetButton;
 private JProgressBar progressBar;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(500, 280);
 frame.setLocationRelativeTo(null);
 frame.setLayout(new BorderLayout(10, 10));

 label = new JLabel("00:00", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 40));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 startButton = new JButton("Start");
 stopButton = new JButton("Stop");
 resetButton = new JButton("Reset");

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");

 stopButton.setEnabled(false);
 resetButton.setEnabled(false);

 JPanel inputPanel = new JPanel();
 inputPanel.add(new JLabel("Minuten:"));
 inputPanel.add(minutesField);
 inputPanel.add(new JLabel("Sekunden:"));
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel();
 buttonPanel.add(startButton);
 buttonPanel.add(stopButton);
 buttonPanel.add(resetButton);

 JPanel centerPanel = new JPanel(new BorderLayout(10, 10));
 centerPanel.add(label, BorderLayout.CENTER);
 centerPanel.add(progressBar, BorderLayout.SOUTH);

 frame.add(centerPanel, BorderLayout.CENTER);
 frame.add(inputPanel, BorderLayout.NORTH);
 frame.add(buttonPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateLabelAndProgress();

 if (remainingSeconds <= 0) {
 timer.stop();
 Toolkit.getDefaultToolkit().beep();
 label.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(true);
 }
 });

 startButton.addActionListener(e -> {
 if (!timer.isRunning()) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 label.setText("Ungültige Eingabe");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 label.setText("Bitte Zeit > 0 eingeben");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 label.setText("Ungültige Eingabe");
 return;
 }
 }

 updateLabelAndProgress();
 timer.start();
 startButton.setEnabled(false);
 stopButton.setEnabled(true);
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 }
 });

 stopButton.addActionListener(e -> {
 if (timer.isRunning()) {
 timer.stop();
 updateLabelAndProgress();
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 remainingSeconds = initialSeconds;
 updateLabelAndProgress();
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startButton.setEnabled(true);
 stopButton.setEnabled(false);
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateLabelAndProgress() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 label.setText(String.format("%02d:%02d", minutes, seconds));

 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 } else {
 progressBar.setValue(0);
 progressBar.setString("0%");
 }
 }
}


---------


import javax.swing.*;
import java.awt.*;

public class CountdownMitPause {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownMitPause().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel label;
 private JTextField minutesField;
 private JTextField secondsField;
 private JButton startPauseButton;
 private JButton resetButton;
 private JProgressBar progressBar;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(520, 280);
 frame.setLocationRelativeTo(null);
 frame.setLayout(new BorderLayout(10, 10));

 label = new JLabel("00:00", SwingConstants.CENTER);
 label.setFont(new Font("Arial", Font.BOLD, 40));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 startPauseButton = new JButton("Start");
 resetButton = new JButton("Reset");

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");

 resetButton.setEnabled(false);

 JPanel inputPanel = new JPanel();
 inputPanel.add(new JLabel("Minuten:"));
 inputPanel.add(minutesField);
 inputPanel.add(new JLabel("Sekunden:"));
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel();
 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel centerPanel = new JPanel(new BorderLayout(10, 10));
 centerPanel.add(label, BorderLayout.CENTER);
 centerPanel.add(progressBar, BorderLayout.SOUTH);

 frame.add(centerPanel, BorderLayout.CENTER);
 frame.add(inputPanel, BorderLayout.NORTH);
 frame.add(buttonPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateLabelAndProgress();

 if (remainingSeconds <= 0) {
 timer.stop();
 Toolkit.getDefaultToolkit().beep();
 label.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 label.setText("Ungültige Eingabe");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 label.setText("Bitte Zeit > 0 eingeben");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 label.setText("Ungültige Eingabe");
 return;
 }
 }

 updateLabelAndProgress();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateLabelAndProgress();
 label.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateLabelAndProgress() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 label.setText(String.format("%02d:%02d", minutes, seconds));

 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 } else {
 progressBar.setValue(0);
 progressBar.setString("0%");
 }
 }
}


-------

import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;

public class CountdownMitSchickemLayout {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownMitSchickemLayout().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private JButton startPauseButton;
 private JButton resetButton;
 private JProgressBar progressBar;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(520, 360);
 frame.setLocationRelativeTo(null);
 frame.setResizable(false);

 JPanel root = new JPanel(new BorderLayout(15, 15));
 root.setBorder(new EmptyBorder(20, 20, 20, 20));
 root.setBackground(new Color(245, 247, 250));

 JLabel title = new JLabel("Countdown", SwingConstants.CENTER);
 title.setFont(new Font("SansSerif", Font.BOLD, 28));
 title.setForeground(new Color(40, 40, 40));

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 56));
 timeLabel.setForeground(new Color(25, 118, 210));

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setPreferredSize(new Dimension(0, 24));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 10, 10));
 centerPanel.setOpaque(false);
 centerPanel.add(title);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField);
 styleField(secondsField);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 10, 0));
 inputPanel.setOpaque(false);
 inputPanel.add(new JLabel("Minuten:"));
 inputPanel.add(minutesField);
 inputPanel.add(new JLabel("Sekunden:"));
 inputPanel.add(secondsField);

 startPauseButton = new JButton("Start");
 resetButton = new JButton("Reset");
 styleButton(startPauseButton, new Color(46, 125, 50));
 styleButton(resetButton, new Color(198, 40, 40));
 resetButton.setEnabled(false);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 buttonPanel.setOpaque(false);
 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 root.add(centerPanel, BorderLayout.CENTER);
 root.add(bottomPanel, BorderLayout.SOUTH);

 frame.setContentPane(root);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();

 if (remainingSeconds <= 0) {
 timer.stop();
 Toolkit.getDefaultToolkit().beep();
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }

 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));

 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void styleField(JTextField field) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 30));
 }

 private void styleButton(JButton button, Color color) {
 button.setFocusPainted(false);
 button.setBackground(color);
 button.setForeground(Color.WHITE);
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setPreferredSize(new Dimension(120, 36));
 }
}


----------


import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;

public class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 setForeground(Color.WHITE);
 setFont(new Font("SansSerif", Font.BOLD, 14));

 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }

 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new java.awt.geom.Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
}

-------

RoundButton startButton = new RoundButton("Start");
startButton.setBackground(new Color(46, 125, 50));
startButton.setPreferredSize(new Dimension(80, 80));

----------


import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.geom.Ellipse2D;

public class CountdownDarkMode {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownDarkMode().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private RoundButton startPauseButton;
 private RoundButton resetButton;
 private JProgressBar progressBar;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(560, 380);
 frame.setLocationRelativeTo(null);
 frame.setResizable(false);

 JPanel root = new JPanel(new BorderLayout(15, 15));
 root.setBorder(new EmptyBorder(20, 20, 20, 20));
 root.setBackground(bg);

 JLabel title = new JLabel("Countdown", SwingConstants.CENTER);
 title.setFont(new Font("SansSerif", Font.BOLD, 30));
 title.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);
 centerPanel.add(title);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField, fieldBg, text);
 styleField(secondsField, fieldBg, text);

 JLabel minLabel = new JLabel("Minuten:");
 JLabel secLabel = new JLabel("Sekunden:");
 styleLabel(minLabel);
 styleLabel(secLabel);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);
 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 startPauseButton = new RoundButton("Start");
 resetButton = new RoundButton("Reset");
 styleRoundButton(startPauseButton, green, text);
 styleRoundButton(resetButton, red, text);
 resetButton.setEnabled(false);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);
 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 root.add(centerPanel, BorderLayout.CENTER);
 root.add(bottomPanel, BorderLayout.SOUTH);
 frame.setContentPane(root);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();
 if (remainingSeconds <= 0) {
 timer.stop();
 Toolkit.getDefaultToolkit().beep();
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());
 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }
 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }
 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));
 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void styleField(JTextField field, Color bg, Color fg) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(bg);
 field.setForeground(fg);
 field.setCaretColor(fg);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleLabel(JLabel label) {
 label.setForeground(text);
 label.setFont(new Font("SansSerif", Font.PLAIN, 15));
 }

 private void styleRoundButton(RoundButton button, Color color, Color fg) {
 button.setBackground(color);
 button.setForeground(fg);
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setPreferredSize(new Dimension(110, 110));
 }

 static class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }
 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
 }
}


-------
----------
import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.geom.Ellipse2D;

public class CountdownDarkMode {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownDarkMode().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private RoundButton startPauseButton;
 private RoundButton resetButton;
 private JProgressBar progressBar;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(560, 380);
 frame.setLocationRelativeTo(null);
 frame.setResizable(false);

 JPanel root = new JPanel(new BorderLayout(15, 15));
 root.setBorder(new EmptyBorder(20, 20, 20, 20));
 root.setBackground(bg);

 JLabel title = new JLabel("Countdown", SwingConstants.CENTER);
 title.setFont(new Font("SansSerif", Font.BOLD, 30));
 title.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);
 centerPanel.add(title);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField, fieldBg, text);
 styleField(secondsField, fieldBg, text);

 JLabel minLabel = new JLabel("Minuten:");
 JLabel secLabel = new JLabel("Sekunden:");
 styleLabel(minLabel);
 styleLabel(secLabel);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);
 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 startPauseButton = new RoundButton("Start");
 resetButton = new RoundButton("Reset");
 styleRoundButton(startPauseButton, green, text);
 styleRoundButton(resetButton, red, text);
 resetButton.setEnabled(false);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);
 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 root.add(centerPanel, BorderLayout.CENTER);
 root.add(bottomPanel, BorderLayout.SOUTH);
 frame.setContentPane(root);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();
 if (remainingSeconds <= 0) {
 timer.stop();
 Toolkit.getDefaultToolkit().beep();
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());
 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }
 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }
 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));
 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void styleField(JTextField field, Color bg, Color fg) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(bg);
 field.setForeground(fg);
 field.setCaretColor(fg);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleLabel(JLabel label) {
 label.setForeground(text);
 label.setFont(new Font("SansSerif", Font.PLAIN, 15));
 }

 private void styleRoundButton(RoundButton button, Color color, Color fg) {
 button.setBackground(color);
 button.setForeground(fg);
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setPreferredSize(new Dimension(110, 110));
 }

 static class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }
 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
 }
}

--------
---------
import javax.sound.sampled.*;
import java.io.File;
import java.io.IOException;

public class SoundPlayer {
 public static void playSound(String filePath) {
 try {
 AudioInputStream audioInputStream = AudioSystem.getAudioInputStream(new File(filePath));
 Clip clip = AudioSystem.getClip();
 clip.open(audioInputStream);
 clip.start();
 } catch (UnsupportedAudioFileException | IOException | LineUnavailableException e) {
 e.printStackTrace();
 }
 }
}

-----

SoundPlayer.playSound("alarm.wav");

----------

import javax.sound.sampled.AudioInputStream;
import javax.sound.sampled.AudioSystem;
import javax.sound.sampled.Clip;
import javax.sound.sampled.LineUnavailableException;
import javax.sound.sampled.UnsupportedAudioFileException;
import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.geom.Ellipse2D;
import java.io.File;
import java.io.IOException;

public class CountdownDarkMode {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownDarkMode().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private RoundButton startPauseButton;
 private RoundButton resetButton;
 private JProgressBar progressBar;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(560, 380);
 frame.setLocationRelativeTo(null);
 frame.setResizable(false);

 JPanel root = new JPanel(new BorderLayout(15, 15));
 root.setBorder(new EmptyBorder(20, 20, 20, 20));
 root.setBackground(bg);

 JLabel title = new JLabel("Countdown", SwingConstants.CENTER);
 title.setFont(new Font("SansSerif", Font.BOLD, 30));
 title.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);
 centerPanel.add(title);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField, fieldBg, text);
 styleField(secondsField, fieldBg, text);

 JLabel minLabel = new JLabel("Minuten:");
 JLabel secLabel = new JLabel("Sekunden:");
 styleLabel(minLabel);
 styleLabel(secLabel);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);
 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 startPauseButton = new RoundButton("Start");
 resetButton = new RoundButton("Reset");
 styleRoundButton(startPauseButton, green, text);
 styleRoundButton(resetButton, red, text);
 resetButton.setEnabled(false);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);
 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 root.add(centerPanel, BorderLayout.CENTER);
 root.add(bottomPanel, BorderLayout.SOUTH);
 frame.setContentPane(root);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();
 if (remainingSeconds <= 0) {
 timer.stop();
 playSound("alarm.wav");
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());
 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }
 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }
 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));
 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void playSound(String filePath) {
 try {
 AudioInputStream audioInputStream = AudioSystem.getAudioInputStream(new File(filePath));
 Clip clip = AudioSystem.getClip();
 clip.open(audioInputStream);
 clip.start();
 } catch (UnsupportedAudioFileException | IOException | LineUnavailableException e) {
 e.printStackTrace();
 }
 }

 private void styleField(JTextField field, Color bg, Color fg) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(bg);
 field.setForeground(fg);
 field.setCaretColor(fg);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleLabel(JLabel label) {
 label.setForeground(text);
 label.setFont(new Font("SansSerif", Font.PLAIN, 15));
 }

 private void styleRoundButton(RoundButton button, Color color, Color fg) {
 button.setBackground(color);
 button.setForeground(fg);
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setPreferredSize(new Dimension(110, 110));
 }

 static class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }
 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
 }
}

---------
----------

import javazoom.jl.player.Player;
import java.io.FileInputStream;

public class Mp3Player {
 public static void playMp3(String filePath) {
 new Thread(() -> {
 try (FileInputStream fis = new FileInputStream(filePath)) {
 Player player = new Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
 }
}
------------

Mp3Player.playMp3("alarm.mp3");

--------
---------

import javazoom.jl.player.Player;
import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.geom.Ellipse2D;
import java.io.FileInputStream;

public class CountdownDarkModeMp3 {
 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> new CountdownDarkModeMp3().createAndShowGUI());
 }

 private JFrame frame;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private RoundButton startPauseButton;
 private RoundButton resetButton;
 private JProgressBar progressBar;
 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 private void createAndShowGUI() {
 frame = new JFrame("Countdown");
 frame.setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 frame.setSize(560, 380);
 frame.setLocationRelativeTo(null);
 frame.setResizable(false);

 JPanel root = new JPanel(new BorderLayout(15, 15));
 root.setBorder(new EmptyBorder(20, 20, 20, 20));
 root.setBackground(bg);

 JLabel title = new JLabel("Countdown", SwingConstants.CENTER);
 title.setFont(new Font("SansSerif", Font.BOLD, 30));
 title.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);
 centerPanel.add(title);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField, fieldBg, text);
 styleField(secondsField, fieldBg, text);

 JLabel minLabel = new JLabel("Minuten:");
 JLabel secLabel = new JLabel("Sekunden:");
 styleLabel(minLabel);
 styleLabel(secLabel);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);
 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 startPauseButton = new RoundButton("Start");
 resetButton = new RoundButton("Reset");
 styleRoundButton(startPauseButton, green, text);
 styleRoundButton(resetButton, red, text);
 resetButton.setEnabled(false);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);
 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 root.add(centerPanel, BorderLayout.CENTER);
 root.add(bottomPanel, BorderLayout.SOUTH);
 frame.setContentPane(root);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();
 if (remainingSeconds <= 0) {
 timer.stop();
 playMp3("alarm.mp3");
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());
 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }
 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }
 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }
 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });

 frame.setVisible(true);
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));
 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void playMp3(String filePath) {
 new Thread(() -> {
 try (FileInputStream fis = new FileInputStream(filePath)) {
 Player player = new Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
 }

 private void styleField(JTextField field, Color bg, Color fg) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(bg);
 field.setForeground(fg);
 field.setCaretColor(fg);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleLabel(JLabel label) {
 label.setForeground(text);
 label.setFont(new Font("SansSerif", Font.PLAIN, 15));
 }

 private void styleRoundButton(RoundButton button, Color color, Color fg) {
 button.setBackground(color);
 button.setForeground(fg);
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setPreferredSize(new Dimension(110, 110));
 }

 static class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);
 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }
 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
 }
}

----------
----------

private void playMp3(String filePath) {
 new Thread(() -> {
 try (FileInputStream fis = new FileInputStream(filePath)) {
 Player player = new Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
}

------


private void playMp3(String filePath) {
 new Thread(() -> {
 try (java.io.FileInputStream fis = new java.io.FileInputStream(filePath)) {
 javazoom.jl.player.Player player = new javazoom.jl.player.Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
}

----------


private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));
}

---------
----------

import javazoom.jl.player.Player;

import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.io.FileInputStream;

public class CountdownDarkModeMp3 extends JFrame {

 private JPanel contentPane;
 private JLabel titleLabel;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private JButton startPauseButton;
 private JButton resetButton;
 private JProgressBar progressBar;

 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> {
 try {
 CountdownDarkModeMp3 frame = new CountdownDarkModeMp3();
 frame.setVisible(true);
 } catch (Exception e) {
 e.printStackTrace();
 }
 });
 }

 public CountdownDarkModeMp3() {
 setTitle("Countdown");
 setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 setBounds(100, 100, 560, 380);
 setLocationRelativeTo(null);
 setResizable(false);

 contentPane = new JPanel();
 contentPane.setBorder(new EmptyBorder(20, 20, 20, 20));
 contentPane.setBackground(bg);
 setContentPane(contentPane);
 contentPane.setLayout(new BorderLayout(15, 15));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);

 titleLabel = new JLabel("Countdown", SwingConstants.CENTER);
 titleLabel.setFont(new Font("SansSerif", Font.BOLD, 30));
 titleLabel.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 centerPanel.add(titleLabel);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);

 JLabel minLabel = new JLabel("Minuten:");
 minLabel.setForeground(text);
 minLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 JLabel secLabel = new JLabel("Sekunden:");
 secLabel.setForeground(text);
 secLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField);
 styleField(secondsField);

 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);

 startPauseButton = new JButton("Start");
 resetButton = new JButton("Reset");
 styleButton(startPauseButton, green);
 styleButton(resetButton, red);
 resetButton.setEnabled(false);

 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 contentPane.add(centerPanel, BorderLayout.CENTER);
 contentPane.add(bottomPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();

 if (remainingSeconds <= 0) {
 timer.stop();
 playMp3("alarm.mp3");
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }

 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }

 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));

 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void playMp3(String filePath) {
 new Thread(() -> {
 try (FileInputStream fis = new FileInputStream(filePath)) {
 Player player = new Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
 }

 private void styleField(JTextField field) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(fieldBg);
 field.setForeground(text);
 field.setCaretColor(text);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleButton(JButton button, Color color) {
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setForeground(Color.WHITE);
 button.setBackground(color);
 button.setFocusPainted(false);
 button.setPreferredSize(new Dimension(120, 40));
 }
}

---------
---------

import javazoom.jl.player.Player;

import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.io.FileInputStream;

public class CountdownWindowBuilder extends JFrame {

 private JPanel contentPane;
 private JLabel titleLabel;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private JButton startPauseButton;
 private JButton resetButton;
 private JProgressBar progressBar;

 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> {
 try {
 CountdownWindowBuilder frame = new CountdownWindowBuilder();
 frame.setVisible(true);
 } catch (Exception e) {
 e.printStackTrace();
 }
 });
 }

 public CountdownWindowBuilder() {
 setTitle("Countdown");
 setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 setBounds(100, 100, 560, 380);
 setLocationRelativeTo(null);
 setResizable(false);

 contentPane = new JPanel();
 contentPane.setBorder(new EmptyBorder(20, 20, 20, 20));
 contentPane.setBackground(bg);
 setContentPane(contentPane);
 contentPane.setLayout(new BorderLayout(15, 15));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);

 titleLabel = new JLabel("Countdown", SwingConstants.CENTER);
 titleLabel.setFont(new Font("SansSerif", Font.BOLD, 30));
 titleLabel.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 centerPanel.add(titleLabel);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);

 JLabel minLabel = new JLabel("Minuten:");
 minLabel.setForeground(text);
 minLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 JLabel secLabel = new JLabel("Sekunden:");
 secLabel.setForeground(text);
 secLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField);
 styleField(secondsField);

 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);

 startPauseButton = new JButton("Start");
 resetButton = new JButton("Reset");
 styleButton(startPauseButton, green);
 styleButton(resetButton, red);
 resetButton.setEnabled(false);

 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 contentPane.add(centerPanel, BorderLayout.CENTER);
 contentPane.add(bottomPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();

 if (remainingSeconds <= 0) {
 timer.stop();
 playMp3("alarm.mp3");
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }

 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }

 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));

 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void playMp3(String filePath) {
 new Thread(() -> {
 try (FileInputStream fis = new FileInputStream(filePath)) {
 Player player = new Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
 }

 private void styleField(JTextField field) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(fieldBg);
 field.setForeground(text);
 field.setCaretColor(text);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleButton(JButton button, Color color) {
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setForeground(Color.WHITE);
 button.setBackground(color);
 button.setFocusPainted(false);
 button.setPreferredSize(new Dimension(120, 40));
 }
}

--------
---------

import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.geom.Ellipse2D;

public class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 setForeground(Color.WHITE);
 setFont(new Font("SansSerif", Font.BOLD, 14));

 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }

 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
}


-------

private JButton startPauseButton;
private JButton resetButton;


private RoundButton startPauseButton;
private RoundButton resetButton;

startPauseButton = new JButton("Start");
resetButton = new JButton("Reset");

startPauseButton = new RoundButton("Start");
resetButton = new RoundButton("Reset");


startPauseButton.setPreferredSize(new Dimension(90, 90));
resetButton.setPreferredSize(new Dimension(90, 90));

startPauseButton.setBackground(new Color(46, 125, 50));
resetButton.setBackground(new Color(198, 40, 40));

startPauseButton = new RoundButton("Start");
startPauseButton.setBackground(new Color(46, 125, 50));
startPauseButton.setPreferredSize(new Dimension(90, 90));

resetButton = new RoundButton("Reset");
resetButton.setBackground(new Color(198, 40, 40));
resetButton.setPreferredSize(new Dimension(90, 90));


-------
-------

import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.geom.Ellipse2D;

public class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 setForeground(Color.WHITE);
 setFont(new Font("SansSerif", Font.BOLD, 14));

 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }

 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
}

---------
---------

import javazoom.jl.player.Player;

import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.io.FileInputStream;

public class CountdownWindowBuilder extends JFrame {

 private JPanel contentPane;
 private JLabel titleLabel;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private RoundButton startPauseButton;
 private RoundButton resetButton;
 private JProgressBar progressBar;

 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> {
 try {
 CountdownWindowBuilder frame = new CountdownWindowBuilder();
 frame.setVisible(true);
 } catch (Exception e) {
 e.printStackTrace();
 }
 });
 }

 public CountdownWindowBuilder() {
 setTitle("Countdown");
 setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 setBounds(100, 100, 560, 420);
 setLocationRelativeTo(null);
 setResizable(false);

 contentPane = new JPanel();
 contentPane.setBorder(new EmptyBorder(20, 20, 20, 20));
 contentPane.setBackground(bg);
 setContentPane(contentPane);
 contentPane.setLayout(new BorderLayout(15, 15));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);

 titleLabel = new JLabel("Countdown", SwingConstants.CENTER);
 titleLabel.setFont(new Font("SansSerif", Font.BOLD, 30));
 titleLabel.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 centerPanel.add(titleLabel);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);

 JLabel minLabel = new JLabel("Minuten:");
 minLabel.setForeground(text);
 minLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 JLabel secLabel = new JLabel("Sekunden:");
 secLabel.setForeground(text);
 secLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField);
 styleField(secondsField);

 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);

 startPauseButton = new RoundButton("Start");
 resetButton = new RoundButton("Reset");
 styleRoundButton(startPauseButton, green);
 styleRoundButton(resetButton, red);
 resetButton.setEnabled(false);

 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 contentPane.add(centerPanel, BorderLayout.CENTER);
 contentPane.add(bottomPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();

 if (remainingSeconds <= 0) {
 timer.stop();
 playMp3("alarm.mp3");
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }

 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }

 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));

 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void playMp3(String filePath) {
 new Thread(() -> {
 try (FileInputStream fis = new FileInputStream(filePath)) {
 Player player = new Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
 }

 private void styleField(JTextField field) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(fieldBg);
 field.setForeground(text);
 field.setCaretColor(text);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleRoundButton(RoundButton button, Color color) {
 button.setBackground(color);
 button.setForeground(Color.WHITE);
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setPreferredSize(new Dimension(90, 90));
 }
}
----
----

titleLabel.setFont(new Font("SansSerif", Font.BOLD, 30));
timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
minLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));
secLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));
minutesField.setFont(new Font("SansSerif", Font.PLAIN, 16));
secondsField.setFont(new Font("SansSerif", Font.PLAIN, 16));
startPauseButton.setFont(new Font("SansSerif", Font.BOLD, 14));
resetButton.setFont(new Font("SansSerif", Font.BOLD, 14));

setBounds(100, 100, 560, 420);
contentPane.setBorder(new EmptyBorder(20, 20, 20, 20));
progressBar.setPreferredSize(new Dimension(0, 26));
minutesField.setPreferredSize(new Dimension(60, 32));
secondsField.setPreferredSize(new Dimension(60, 32));
startPauseButton.setPreferredSize(new Dimension(90, 90));
resetButton.setPreferredSize(new Dimension(90, 90));

---------------
---------------

# kompletter Code

import javazoom.jl.player.Player;

import javax.swing.*;
import javax.swing.border.EmptyBorder;
import java.awt.*;
import java.io.FileInputStream;

public class CountdownWindowBuilder extends JFrame {

 private JPanel contentPane;
 private JLabel titleLabel;
 private JLabel timeLabel;
 private JTextField minutesField;
 private JTextField secondsField;
 private RoundButton startPauseButton;
 private RoundButton resetButton;
 private JProgressBar progressBar;

 private Timer timer;
 private int initialSeconds = 0;
 private int remainingSeconds = 0;
 private boolean running = false;

 private final Color bg = new Color(24, 24, 28);
 private final Color panelBg = new Color(32, 32, 38);
 private final Color text = new Color(235, 235, 235);
 private final Color accent = new Color(70, 130, 255);
 private final Color green = new Color(46, 125, 50);
 private final Color red = new Color(198, 40, 40);
 private final Color fieldBg = new Color(45, 45, 52);

 public static void main(String[] args) {
 SwingUtilities.invokeLater(() -> {
 try {
 CountdownWindowBuilder frame = new CountdownWindowBuilder();
 frame.setVisible(true);
 } catch (Exception e) {
 e.printStackTrace();
 }
 });
 }

 public CountdownWindowBuilder() {
 setTitle("Countdown");
 setDefaultCloseOperation(JFrame.EXIT_ON_CLOSE);
 setBounds(100, 100, 560, 420);
 setLocationRelativeTo(null);
 setResizable(false);

 contentPane = new JPanel();
 contentPane.setBorder(new EmptyBorder(20, 20, 20, 20));
 contentPane.setBackground(bg);
 setContentPane(contentPane);
 contentPane.setLayout(new BorderLayout(15, 15));

 JPanel centerPanel = new JPanel(new GridLayout(3, 1, 12, 12));
 centerPanel.setOpaque(false);

 titleLabel = new JLabel("Countdown", SwingConstants.CENTER);
 titleLabel.setFont(new Font("SansSerif", Font.BOLD, 30));
 titleLabel.setForeground(text);

 timeLabel = new JLabel("00:00", SwingConstants.CENTER);
 timeLabel.setFont(new Font("SansSerif", Font.BOLD, 58));
 timeLabel.setForeground(accent);

 progressBar = new JProgressBar(0, 100);
 progressBar.setStringPainted(true);
 progressBar.setValue(100);
 progressBar.setString("100%");
 progressBar.setForeground(accent);
 progressBar.setBackground(panelBg);
 progressBar.setBorderPainted(false);
 progressBar.setPreferredSize(new Dimension(0, 26));

 centerPanel.add(titleLabel);
 centerPanel.add(timeLabel);
 centerPanel.add(progressBar);

 JPanel inputPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 12, 0));
 inputPanel.setOpaque(false);

 JLabel minLabel = new JLabel("Minuten:");
 minLabel.setForeground(text);
 minLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 JLabel secLabel = new JLabel("Sekunden:");
 secLabel.setForeground(text);
 secLabel.setFont(new Font("SansSerif", Font.PLAIN, 15));

 minutesField = new JTextField("0", 4);
 secondsField = new JTextField("10", 4);
 styleField(minutesField);
 styleField(secondsField);

 inputPanel.add(minLabel);
 inputPanel.add(minutesField);
 inputPanel.add(secLabel);
 inputPanel.add(secondsField);

 JPanel buttonPanel = new JPanel(new FlowLayout(FlowLayout.CENTER, 18, 0));
 buttonPanel.setOpaque(false);

 startPauseButton = new RoundButton("Start");
 resetButton = new RoundButton("Reset");
 styleRoundButton(startPauseButton, green);
 styleRoundButton(resetButton, red);
 resetButton.setEnabled(false);

 buttonPanel.add(startPauseButton);
 buttonPanel.add(resetButton);

 JPanel bottomPanel = new JPanel(new GridLayout(2, 1, 10, 10));
 bottomPanel.setOpaque(false);
 bottomPanel.add(inputPanel);
 bottomPanel.add(buttonPanel);

 contentPane.add(centerPanel, BorderLayout.CENTER);
 contentPane.add(bottomPanel, BorderLayout.SOUTH);

 timer = new Timer(1000, e -> {
 remainingSeconds--;
 updateDisplay();

 if (remainingSeconds <= 0) {
 timer.stop();
 playMp3("alarm.mp3");
 timeLabel.setText("Fertig!");
 progressBar.setValue(0);
 progressBar.setString("0%");
 running = false;
 startPauseButton.setText("Start");
 }
 });

 startPauseButton.addActionListener(e -> {
 if (!running) {
 if (remainingSeconds <= 0) {
 try {
 int minutes = Integer.parseInt(minutesField.getText().trim());
 int seconds = Integer.parseInt(secondsField.getText().trim());

 if (minutes < 0 || seconds < 0 || seconds > 59) {
 timeLabel.setText("Ungültig");
 return;
 }

 initialSeconds = minutes * 60 + seconds;
 if (initialSeconds <= 0) {
 timeLabel.setText("Zeit > 0");
 return;
 }

 remainingSeconds = initialSeconds;
 } catch (NumberFormatException ex) {
 timeLabel.setText("Ungültig");
 return;
 }
 }

 updateDisplay();
 timer.start();
 running = true;
 startPauseButton.setText("Pause");
 resetButton.setEnabled(true);
 minutesField.setEnabled(false);
 secondsField.setEnabled(false);
 } else {
 timer.stop();
 running = false;
 startPauseButton.setText("Fortsetzen");
 }
 });

 resetButton.addActionListener(e -> {
 timer.stop();
 running = false;
 remainingSeconds = initialSeconds;
 updateDisplay();
 timeLabel.setText("00:00");
 progressBar.setValue(100);
 progressBar.setString("100%");
 minutesField.setEnabled(true);
 secondsField.setEnabled(true);
 startPauseButton.setText("Start");
 resetButton.setEnabled(false);
 });
 }

 private void updateDisplay() {
 int minutes = remainingSeconds / 60;
 int seconds = remainingSeconds % 60;
 timeLabel.setText(String.format("%02d:%02d", minutes, seconds));

 if (initialSeconds > 0) {
 int percent = (int) ((remainingSeconds * 100.0) / initialSeconds);
 progressBar.setValue(percent);
 progressBar.setString(percent + "%");
 }
 }

 private void playMp3(String filePath) {
 new Thread(() -> {
 try (FileInputStream fis = new FileInputStream(filePath)) {
 Player player = new Player(fis);
 player.play();
 } catch (Exception e) {
 e.printStackTrace();
 }
 }).start();
 }

 private void styleField(JTextField field) {
 field.setFont(new Font("SansSerif", Font.PLAIN, 16));
 field.setHorizontalAlignment(JTextField.CENTER);
 field.setPreferredSize(new Dimension(60, 32));
 field.setBackground(fieldBg);
 field.setForeground(text);
 field.setCaretColor(text);
 field.setBorder(BorderFactory.createLineBorder(new Color(80, 80, 90), 1));
 }

 private void styleRoundButton(RoundButton button, Color color) {
 button.setBackground(color);
 button.setForeground(Color.WHITE);
 button.setFont(new Font("SansSerif", Font.BOLD, 14));
 button.setPreferredSize(new Dimension(90, 90));
 }
}

---------
import javax.swing.*;
import java.awt.*;
import java.awt.event.MouseAdapter;
import java.awt.event.MouseEvent;
import java.awt.geom.Ellipse2D;

public class RoundButton extends JButton {
 private Shape shape;

 public RoundButton(String text) {
 super(text);
 setContentAreaFilled(false);
 setFocusPainted(false);
 setBorderPainted(false);
 setOpaque(false);
 setForeground(Color.WHITE);
 setFont(new Font("SansSerif", Font.BOLD, 14));

 addMouseListener(new MouseAdapter() {
 @Override
 public void mouseEntered(MouseEvent e) {
 setCursor(Cursor.getPredefinedCursor(Cursor.HAND_CURSOR));
 }
 });
 }

 @Override
 protected void paintComponent(Graphics g) {
 Graphics2D g2 = (Graphics2D) g.create();
 g2.setRenderingHint(RenderingHints.KEY_ANTIALIASING, RenderingHints.VALUE_ANTIALIAS_ON);

 if (getModel().isArmed()) {
 g2.setColor(getBackground().darker());
 } else if (getModel().isRollover()) {
 g2.setColor(getBackground().brighter());
 } else {
 g2.setColor(getBackground());
 }

 g2.fillOval(0, 0, getWidth(), getHeight());
 super.paintComponent(g2);
 g2.dispose();
 }

 @Override
 public boolean contains(int x, int y) {
 if (shape == null || !shape.getBounds().equals(getBounds())) {
 shape = new Ellipse2D.Float(0, 0, getWidth(), getHeight());
 }
 return shape.contains(x, y);
 }
}

MeinProjekt/
├─ src/
│ ├─ CountdownWindowBuilder.java
│ └─ RoundButton.java
├─ alarm.mp3
└─ lib/
 └─ jl1.0.1.jar


