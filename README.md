#  Liquid Glass Framework (Pure Native Edition)

## A minimalist, high-performance UI framework that achieves the Apple "Liquid Glass" (iOS 26 / VisionOS) aesthetic using pure native rendering.

This framework uses PySide6 native painting (QPainter) combined with direct Windows API hooks (SetWindowCompositionAttribute) to force true, real-time hardware background compositing.

### Dependencies

pip install PySide6


### Architecture

The framework is divided into two perfectly separated files:

liquid_engine.py: This is the reusable brain of the framework. It handles the undocumented OS-level API calls to force the Acrylic blur. It also contains the LiquidGlassPanel widget, which uses native C++ style drawing to create the subtle physical edges and translucency of the glass.

app.py: Your main application file. It imports the engine and builds the UI layout using standard PySide6 widgets (Buttons, Labels, Layouts).

### How to Export & Use in Other Projects

Step 1: Drop in the Engine

Copy the liquid_engine.py file and paste it into the root directory of your new project. You do not need to modify this file.

Step 2: Import and Inherit

In your new project's main Python file, import the base window and the panel widget from the engine. Set up your main class to inherit from LiquidMainWindow.

    import sys
    from PySide6.QtWidgets import QApplication, QVBoxLayout, QLabel
    from liquid_engine import LiquidMainWindow, LiquidGlassPanel

    class MyNewApp(LiquidMainWindow):
      def __init__(self):
        super().__init__()
        self.resize(800, 600)
        
        # Create a layout and apply the LiquidGlassPanel
        main_layout = QVBoxLayout(self)
        
        glass_card = LiquidGlassPanel(radius=16)
        card_layout = QVBoxLayout(glass_card)
        
        # Add standard PySide6 widgets inside the glass card
        text = QLabel("Hello Liquid Glass!")
        text.setStyleSheet("color: white; font-size: 20px;")
        card_layout.addWidget(text)
        
        main_layout.addWidget(glass_card)

    if __name__ == "__main__":
      app = QApplication(sys.argv)
      window = MyNewApp()
      window.show()
      sys.exit(app.exec())


Step 3: Styling UI Elements

Style individual widgets (like text and buttons) using PySide6's setStyleSheet() method, passing in standard Qt stylesheets. Stick to minimal colors (whites, transparent whites) to let the OS blur do the heavy lifting.

### Advanced: Tuning the Glass

If the blur effect feels too bright or too dark based on the user's wallpaper, you can adjust the tint directly in liquid_engine.py.

Find this line in the apply_os_blur function:

accent.GradientColor = 0x01000000 


0x01000000 = Almost completely clear.

0x40000000 = A subtle dark tint (good for readability on bright screens).

0x40FFFFFF = A subtle white tint (frosted glass look).
