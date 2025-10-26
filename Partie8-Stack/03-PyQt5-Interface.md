# 📖 Partie VIII - Chapitre 3 : PyQt5 - Interface VJ, Effets Vidéo, Threading

## 🎯 Objectif du chapitre

Maîtriser la création d'interfaces utilisateur PyQt5 modernes, l'implémentation d'effets vidéo avancés, et la gestion du threading pour des applications desktop performantes.

## 🖥️ PyQt5 Interface : Interface utilisateur PyQt5

### 1. 🏗️ Modern PyQt5 Architecture : Architecture PyQt5 moderne

```
Tu es un expert PyQt5 et desktop application development.

TA TÂCHE : Concevoir une architecture PyQt5 moderne pour [APPLICATION]

PYQT5 ARCHITECTURE :
1. **Model-View-Controller** : Separation of concerns, MVC pattern
2. **Signal-Slot System** : Event-driven architecture, loose coupling
3. **Custom Widgets** : Reusable UI components, QSS styling
4. **Layout Management** : Responsive layouts, adaptive design
5. **Resource Management** : Memory optimization, cleanup
6. **Threading Integration** : Background tasks, UI responsiveness

CONTRAINTES :
- PyQt5 best practices
- Cross-platform compatibility
- Performance optimization
- Memory management
- Error handling
- Testing capabilities

FORMAT DE SORTIE :
1. PyQt5 architecture design
2. MVC implementation
3. Signal-slot patterns
4. Custom widget development
5. Layout management
6. Threading integration
7. Performance optimization

QUALITÉ : Application PyQt5 moderne, performante, maintenable
```

**Exemple d'architecture PyQt5 :**
```python
# src/core/application.py
from PyQt5.QtWidgets import QApplication, QMainWindow, QStackedWidget
from PyQt5.QtCore import QThread, pyqtSignal, QObject, QTimer
from PyQt5.QtGui import QIcon, QPixmap
import sys
import logging

class VideoJockeyApp(QApplication):
    """Main application class with proper PyQt5 architecture"""

    def __init__(self, argv):
        super().__init__(argv)

        # Setup application properties
        self.setApplicationName("Video Jockey Studio")
        self.setApplicationVersion("1.0.0")
        self.setOrganizationName("Creative Studio")
        self.setOrganizationDomain("creativestudio.com")

        # Setup logging
        self.setup_logging()

        # Initialize components
        self.main_window = None
        self.worker_threads = []
        self.settings = None

        # Setup exception handling
        self.setup_exception_handling()

    def setup_logging(self):
        """Setup logging configuration"""
        logging.basicConfig(
            level=logging.INFO,
            format='%(asctime)s - %(name)s - %(levelname)s - %(message)s',
            handlers=[
                logging.FileHandler('vj_studio.log'),
                logging.StreamHandler()
            ]
        )

        self.logger = logging.getLogger(__name__)

    def setup_exception_handling(self):
        """Setup global exception handling"""
        def exception_handler(exc_type, exc_value, exc_traceback):
            if issubclass(exc_type, KeyboardInterrupt):
                self.logger.info("Application interrupted by user")
                return

            self.logger.error(
                "Unhandled exception",
                exc_info=(exc_type, exc_value, exc_traceback)
            )

            # Show error dialog to user
            from PyQt5.QtWidgets import QMessageBox
            QMessageBox.critical(
                None,
                "Application Error",
                f"An unexpected error occurred:\n{str(exc_value)}\n\nThe application will now close."
            )

            sys.exit(1)

        sys.excepthook = exception_handler

    def start(self):
        """Start the application"""
        try:
            self.logger.info("Starting Video Jockey Studio")

            # Create and show main window
            self.main_window = MainWindow()
            self.main_window.show()

            # Start event loop
            self.logger.info("Application started successfully")
            return self.exec_()

        except Exception as e:
            self.logger.error(f"Failed to start application: {e}")
            raise

    def cleanup(self):
        """Cleanup resources before exit"""
        self.logger.info("Cleaning up application resources")

        # Stop all worker threads
        for thread in self.worker_threads:
            if thread.isRunning():
                thread.stop()
                thread.wait()

        # Save settings
        if self.settings:
            self.settings.save()

        self.logger.info("Application cleanup completed")

# src/ui/main_window.py
class MainWindow(QMainWindow):
    """Main application window with modern PyQt5 patterns"""

    def __init__(self):
        super().__init__()

        # Setup window properties
        self.setWindowTitle("Video Jockey Studio")
        self.setMinimumSize(1200, 800)
        self.setWindowIcon(QIcon("resources/icons/app_icon.png"))

        # Initialize state
        self.current_project = None
        self.video_players = []
        self.effects_processor = None
        self.midi_controller = None

        # Setup UI
        self.setup_ui()
        self.setup_menu_bar()
        self.setup_toolbars()
        self.setup_status_bar()
        self.setup_shortcuts()

        # Connect signals
        self.connect_signals()

        # Load initial state
        self.load_initial_state()

    def setup_ui(self):
        """Setup main UI layout"""
        # Create central widget
        central_widget = QWidget()
        self.setCentralWidget(central_widget)

        # Create main layout
        main_layout = QHBoxLayout(central_widget)

        # Left panel - Project explorer and effects
        left_panel = self.create_left_panel()
        main_layout.addWidget(left_panel, 1)

        # Center panel - Video canvas and controls
        center_panel = self.create_center_panel()
        main_layout.addWidget(center_panel, 3)

        # Right panel - Properties and settings
        right_panel = self.create_right_panel()
        main_layout.addWidget(right_panel, 1)

        # Setup responsive layout
        main_layout.setSpacing(2)
        main_layout.setContentsMargins(4, 4, 4, 4)

    def create_left_panel(self):
        """Create left sidebar with project and effects"""
        panel = QFrame()
        panel.setFrameStyle(QFrame.StyledPanel)
        panel.setMaximumWidth(300)

        layout = QVBoxLayout(panel)

        # Project explorer
        project_group = QGroupBox("Project")
        project_layout = QVBoxLayout(project_group)

        self.project_tree = QTreeWidget()
        self.project_tree.setHeaderHidden(True)
        self.project_tree.itemDoubleClicked.connect(self.on_project_item_double_clicked)

        project_layout.addWidget(self.project_tree)

        # Effects library
        effects_group = QGroupBox("Effects")
        effects_layout = QVBoxLayout(effects_group)

        self.effects_list = QListWidget()
        self.effects_list.itemDoubleClicked.connect(self.on_effect_double_clicked)
        effects_layout.addWidget(self.effects_list)

        layout.addWidget(project_group)
        layout.addWidget(effects_group)
        layout.addStretch()

        return panel

    def create_center_panel(self):
        """Create center area with video canvas"""
        panel = QFrame()
        panel.setFrameStyle(QFrame.StyledPanel)

        layout = QVBoxLayout(panel)

        # Video canvas area
        self.video_canvas = VideoCanvasWidget()
        layout.addWidget(self.video_canvas)

        # Control bar
        control_bar = self.create_control_bar()
        layout.addWidget(control_bar)

        return panel

    def create_control_bar(self):
        """Create video control bar"""
        bar = QFrame()
        bar.setFrameStyle(QFrame.StyledPanel)
        bar.setMaximumHeight(60)

        layout = QHBoxLayout(bar)
        layout.setContentsMargins(8, 4, 8, 4)

        # Playback controls
        self.play_button = QPushButton("▶️ Play")
        self.play_button.clicked.connect(self.on_play_clicked)

        self.stop_button = QPushButton("⏹️ Stop")
        self.stop_button.clicked.connect(self.on_stop_clicked)

        self.record_button = QPushButton("🔴 Record")
        self.record_button.clicked.connect(self.on_record_clicked)

        # Time controls
        self.time_slider = QSlider(Qt.Horizontal)
        self.time_slider.sliderMoved.connect(self.on_time_slider_moved)

        self.time_label = QLabel("00:00:00 / 00:00:00")

        # Volume control
        self.volume_slider = QSlider(Qt.Horizontal)
        self.volume_slider.setMaximum(100)
        self.volume_slider.setValue(80)
        self.volume_slider.sliderMoved.connect(self.on_volume_changed)

        layout.addWidget(self.play_button)
        layout.addWidget(self.stop_button)
        layout.addWidget(self.record_button)
        layout.addStretch()
        layout.addWidget(QLabel("Time:"))
        layout.addWidget(self.time_slider)
        layout.addWidget(self.time_label)
        layout.addStretch()
        layout.addWidget(QLabel("Volume:"))
        layout.addWidget(self.volume_slider)

        return bar

    def create_right_panel(self):
        """Create right sidebar with properties"""
        panel = QFrame()
        panel.setFrameStyle(QFrame.StyledPanel)
        panel.setMaximumWidth(300)

        layout = QVBoxLayout(panel)

        # Video properties
        properties_group = QGroupBox("Properties")
        properties_layout = QVBoxLayout(properties_group)

        self.properties_widget = PropertiesWidget()
        properties_layout.addWidget(self.properties_widget)

        # Layer management
        layers_group = QGroupBox("Layers")
        layers_layout = QVBoxLayout(layers_group)

        self.layers_list = QListWidget()
        self.layers_list.currentItemChanged.connect(self.on_layer_changed)
        layers_layout.addWidget(self.layers_list)

        layout.addWidget(properties_group)
        layout.addWidget(layers_group)
        layout.addStretch()

        return panel

    def setup_menu_bar(self):
        """Setup application menu bar"""
        menubar = self.menuBar()

        # File menu
        file_menu = menubar.addMenu('File')

        new_project_action = QAction('New Project', self)
        new_project_action.setShortcut('Ctrl+N')
        new_project_action.triggered.connect(self.new_project)
        file_menu.addAction(new_project_action)

        open_project_action = QAction('Open Project', self)
        open_project_action.setShortcut('Ctrl+O')
        open_project_action.triggered.connect(self.open_project)
        file_menu.addAction(open_project_action)

        file_menu.addSeparator()

        export_action = QAction('Export Video', self)
        export_action.setShortcut('Ctrl+E')
        export_action.triggered.connect(self.export_video)
        file_menu.addAction(export_action)

        # Edit menu
        edit_menu = menubar.addMenu('Edit')

        undo_action = QAction('Undo', self)
        undo_action.setShortcut('Ctrl+Z')
        undo_action.triggered.connect(self.undo)
        edit_menu.addAction(undo_action)

        redo_action = QAction('Redo', self)
        redo_action.setShortcut('Ctrl+Y')
        redo_action.triggered.connect(self.redo)
        edit_menu.addAction(redo_action)

        # View menu
        view_menu = menubar.addMenu('View')

        fullscreen_action = QAction('Fullscreen', self)
        fullscreen_action.setShortcut('F11')
        fullscreen_action.triggered.connect(self.toggle_fullscreen)
        view_menu.addAction(fullscreen_action)

        # Help menu
        help_menu = menubar.addMenu('Help')

        about_action = QAction('About', self)
        about_action.triggered.connect(self.show_about)
        help_menu.addAction(about_action)
```

### 2. 🎬 Video Effects Engine : Moteur d'effets vidéo

```
Tu es un expert PyQt5 video processing et effects.

TA TÂCHE : Implémenter un moteur d'effets vidéo pour [APPLICATION VJ]

VIDEO EFFECTS SYSTEM :
1. **Real-time Processing** : GPU-accelerated effects, low latency
2. **Effect Chain** : Composable effects, parameter adjustment
3. **Video Mixing** : Multi-layer blending, transitions
4. **Audio Processing** : Audio effects, synchronization
5. **Performance Optimization** : Frame dropping, quality adjustment
6. **Export System** : Video rendering, format conversion

CONTRAINTES :
- Real-time performance
- GPU acceleration
- Memory management
- Threading safety
- Quality preservation

FORMAT DE SORTIE :
1. Video effects architecture
2. Real-time processing
3. Effect chain implementation
4. Performance optimization
5. Memory management
6. Export system
7. Testing strategies

QUALITÉ : Effets vidéo performants, qualitatifs, scalables
```

**Exemple de moteur d'effets vidéo :**
```python
# src/video/effects/effects_engine.py
import cv2
import numpy as np
from PyQt5.QtCore import QThread, pyqtSignal, QMutex, QWaitCondition
from PyQt5.QtGui import QImage, QPixmap
from typing import List, Dict, Any, Optional
import time
import logging

class VideoEffect:
    """Base class for video effects"""

    def __init__(self, name: str, parameters: Dict[str, Any] = None):
        self.name = name
        self.parameters = parameters or {}
        self.enabled = True

    def apply(self, frame: np.ndarray, timestamp: float) -> np.ndarray:
        """Apply effect to video frame"""
        if not self.enabled:
            return frame

        try:
            return self.process_frame(frame, timestamp)
        except Exception as e:
            logging.error(f"Error applying effect {self.name}: {e}")
            return frame

    def process_frame(self, frame: np.ndarray, timestamp: float) -> np.ndarray:
        """Override in subclasses"""
        raise NotImplementedError

    def set_parameter(self, key: str, value: Any):
        """Set effect parameter"""
        self.parameters[key] = value

    def get_parameter(self, key: str, default: Any = None):
        """Get effect parameter"""
        return self.parameters.get(key, default)

class BlurEffect(VideoEffect):
    """Gaussian blur effect"""

    def __init__(self, kernel_size: int = 5, sigma: float = 1.0):
        super().__init__("blur", {"kernel_size": kernel_size, "sigma": sigma})

    def process_frame(self, frame: np.ndarray, timestamp: float) -> np.ndarray:
        kernel_size = self.get_parameter("kernel_size", 5)
        sigma = self.get_parameter("sigma", 1.0)

        if kernel_size % 2 == 0:
            kernel_size += 1  # Must be odd

        return cv2.GaussianBlur(frame, (kernel_size, kernel_size), sigma)

class ColorCorrectionEffect(VideoEffect):
    """Color correction and grading effect"""

    def __init__(self, brightness: float = 0, contrast: float = 1.0,
                 saturation: float = 1.0, hue: float = 0):
        super().__init__("color_correction", {
            "brightness": brightness,
            "contrast": contrast,
            "saturation": saturation,
            "hue": hue
        })

    def process_frame(self, frame: np.ndarray, timestamp: float) -> np.ndarray:
        brightness = self.get_parameter("brightness", 0)
        contrast = self.get_parameter("contrast", 1.0)
        saturation = self.get_parameter("saturation", 1.0)
        hue_shift = self.get_parameter("hue", 0)

        # Convert to HSV for color adjustments
        hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV).astype(np.float32)

        # Adjust brightness
        hsv[:, :, 2] = np.clip(hsv[:, :, 2] + brightness, 0, 255)

        # Adjust saturation
        hsv[:, :, 1] = np.clip(hsv[:, :, 1] * saturation, 0, 255)

        # Adjust hue
        if hue_shift != 0:
            hsv[:, :, 0] = (hsv[:, :, 0] + hue_shift) % 180

        # Adjust contrast
        hsv[:, :, 2] = np.clip((hsv[:, :, 2] - 127.5) * contrast + 127.5, 0, 255)

        # Convert back to BGR
        result = cv2.cvtColor(hsv.astype(np.uint8), cv2.COLOR_HSV2BGR)

        return result

class ChromaKeyEffect(VideoEffect):
    """Green screen / chroma key effect"""

    def __init__(self, key_color: tuple = (0, 255, 0), tolerance: int = 50):
        super().__init__("chroma_key", {
            "key_color": key_color,
            "tolerance": tolerance,
            "smoothness": 0.1
        })

    def process_frame(self, frame: np.ndarray, timestamp: float) -> np.ndarray:
        key_color = self.get_parameter("key_color", (0, 255, 0))
        tolerance = self.get_parameter("tolerance", 50)
        smoothness = self.get_parameter("smoothness", 0.1)

        # Convert key color to numpy array
        key_color = np.array(key_color, dtype=np.uint8)

        # Create mask for key color
        lower_bound = np.clip(key_color - tolerance, 0, 255)
        upper_bound = np.clip(key_color + tolerance, 0, 255)

        mask = cv2.inRange(frame, lower_bound, upper_bound)

        # Smooth mask edges
        if smoothness > 0:
            mask = cv2.GaussianBlur(mask, (0, 0), smoothness * 10)

        # Apply mask
        mask = mask / 255.0
        mask = np.stack([mask] * 3, axis=2)

        # Replace keyed areas with transparency (for composition)
        result = frame.copy()
        result = result.astype(np.float32)
        result = result * (1 - mask) + np.array([0, 0, 0], dtype=np.float32) * mask
        result = result.astype(np.uint8)

        return result

class EffectsEngine:
    """Main effects processing engine"""

    def __init__(self):
        self.effects: List[VideoEffect] = []
        self.effect_chain: List[str] = []
        self.is_processing = False
        self.processing_thread: Optional[EffectsProcessingThread] = None

    def add_effect(self, effect: VideoEffect):
        """Add effect to the chain"""
        self.effects.append(effect)
        self.effect_chain.append(effect.name)

    def remove_effect(self, effect_name: str):
        """Remove effect from chain"""
        self.effects = [e for e in self.effects if e.name != effect_name]
        self.effect_chain = [name for name in self.effect_chain if name != effect_name]

    def reorder_effects(self, new_order: List[str]):
        """Reorder effects in the chain"""
        # Validate new order
        if set(new_order) != set(self.effect_chain):
            raise ValueError("New order must contain the same effects")

        self.effect_chain = new_order

        # Reorder effects list
        effect_map = {effect.name: effect for effect in self.effects}
        self.effects = [effect_map[name] for name in new_order]

    def apply_effects(self, frame: np.ndarray, timestamp: float) -> np.ndarray:
        """Apply all effects in chain to frame"""
        if not self.effects:
            return frame

        result = frame.copy()

        for effect in self.effects:
            if effect.enabled:
                try:
                    result = effect.apply(result, timestamp)
                except Exception as e:
                    logging.error(f"Error applying effect {effect.name}: {e}")
                    # Continue with other effects

        return result

    def get_effect_parameters(self, effect_name: str) -> Dict[str, Any]:
        """Get parameters for specific effect"""
        effect = next((e for e in self.effects if e.name == effect_name), None)
        return effect.parameters if effect else {}

    def set_effect_parameter(self, effect_name: str, parameter: str, value: Any):
        """Set parameter for specific effect"""
        effect = next((e for e in self.effects if e.name == effect_name), None)
        if effect:
            effect.set_parameter(parameter, value)

    def start_real_time_processing(self, video_source: Any):
        """Start real-time effects processing"""
        if self.processing_thread and self.processing_thread.isRunning():
            self.stop_real_time_processing()

        self.processing_thread = EffectsProcessingThread(video_source, self)
        self.processing_thread.start()

    def stop_real_time_processing(self):
        """Stop real-time effects processing"""
        if self.processing_thread:
            self.processing_thread.stop()
            self.processing_thread.wait()
            self.processing_thread = None

class EffectsProcessingThread(QThread):
    """Thread for real-time video effects processing"""

    frame_processed = pyqtSignal(np.ndarray, float)  # frame, timestamp

    def __init__(self, video_source: Any, effects_engine: EffectsEngine):
        super().__init__()
        self.video_source = video_source
        self.effects_engine = effects_engine
        self.is_running = True
        self.mutex = QMutex()
        self.wait_condition = QWaitCondition()

    def run(self):
        """Main processing loop"""
        logging.info("Starting effects processing thread")

        try:
            while self.is_running:
                # Get frame from video source
                ret, frame = self.video_source.read()

                if not ret:
                    logging.warning("No frame received from video source")
                    time.sleep(0.01)  # Small delay to prevent busy waiting
                    continue

                # Get timestamp
                timestamp = time.time()

                # Apply effects
                processed_frame = self.effects_engine.apply_effects(frame, timestamp)

                # Emit processed frame
                self.frame_processed.emit(processed_frame, timestamp)

                # Small delay to maintain frame rate
                time.sleep(0.016)  # ~60 FPS

        except Exception as e:
            logging.error(f"Error in effects processing thread: {e}")
        finally:
            logging.info("Effects processing thread stopped")

    def stop(self):
        """Stop the processing thread"""
        self.mutex.lock()
        self.is_running = False
        self.mutex.unlock()
        self.wait_condition.wakeAll()

# src/video/effects/preset_manager.py
class EffectsPreset:
    """Represents a saved effects preset"""

    def __init__(self, name: str, effects: List[Dict[str, Any]]):
        self.name = name
        self.effects = effects  # List of effect configurations

    def to_dict(self) -> Dict[str, Any]:
        return {
            'name': self.name,
            'effects': self.effects
        }

    @classmethod
    def from_dict(cls, data: Dict[str, Any]) -> 'EffectsPreset':
        return cls(data['name'], data['effects'])

class EffectsPresetManager:
    """Manages effects presets"""

    def __init__(self, presets_file: str = "effects_presets.json"):
        self.presets_file = presets_file
        self.presets: Dict[str, EffectsPreset] = {}
        self.load_presets()

    def save_preset(self, name: str, effects_engine: EffectsEngine):
        """Save current effects as preset"""
        effects_data = []

        for effect in effects_engine.effects:
            effects_data.append({
                'name': effect.name,
                'type': effect.__class__.__name__,
                'parameters': effect.parameters,
                'enabled': effect.enabled
            })

        preset = EffectsPreset(name, effects_data)
        self.presets[name] = preset
        self.save_to_file()

    def load_preset(self, name: str, effects_engine: EffectsEngine) -> bool:
        """Load preset into effects engine"""
        if name not in self.presets:
            return False

        preset = self.presets[name]

        # Clear current effects
        effects_engine.effects.clear()
        effects_engine.effect_chain.clear()

        # Load preset effects
        for effect_data in preset.effects:
            try:
                effect_class = self.get_effect_class(effect_data['type'])
                if effect_class:
                    effect = effect_class()
                    effect.parameters = effect_data['parameters']
                    effect.enabled = effect_data['enabled']

                    effects_engine.add_effect(effect)
            except Exception as e:
                logging.error(f"Error loading effect {effect_data['type']}: {e}")

        return True

    def delete_preset(self, name: str):
        """Delete a preset"""
        if name in self.presets:
            del self.presets[name]
            self.save_to_file()

    def get_preset_names(self) -> List[str]:
        """Get list of available preset names"""
        return list(self.presets.keys())

    def save_to_file(self):
        """Save presets to file"""
        try:
            import json

            presets_data = {
                name: preset.to_dict()
                for name, preset in self.presets.items()
            }

            with open(self.presets_file, 'w') as f:
                json.dump(presets_data, f, indent=2)

        except Exception as e:
            logging.error(f"Error saving presets: {e}")

    def load_presets(self):
        """Load presets from file"""
        try:
            import json

            with open(self.presets_file, 'r') as f:
                presets_data = json.load(f)

            for name, data in presets_data.items():
                self.presets[name] = EffectsPreset.from_dict(data)

        except FileNotFoundError:
            logging.info("No presets file found, starting with empty presets")
        except Exception as e:
            logging.error(f"Error loading presets: {e}")

    def get_effect_class(self, effect_type: str):
        """Get effect class by type name"""
        effect_classes = {
            'BlurEffect': BlurEffect,
            'ColorCorrectionEffect': ColorCorrectionEffect,
            'ChromaKeyEffect': ChromaKeyEffect,
            # Add more effect classes here
        }

        return effect_classes.get(effect_type)
```

## 🧵 Threading : Gestion des threads PyQt5

### 1. 🔄 Advanced Threading Patterns : Patterns de threading avancés

```
Tu es un expert PyQt5 threading et concurrent programming.

TA TÂCHE : Implémenter des patterns de threading avancés pour [APPLICATION]

THREADING PATTERNS :
1. **Worker Threads** : Background processing, non-blocking UI
2. **Producer-Consumer** : Data processing pipelines
3. **Thread Pools** : Controlled concurrency, resource management
4. **Event-Driven** : Signal-slot with threading, async operations
5. **Resource Management** : Memory optimization, cleanup
6. **Error Handling** : Thread-safe error management

CONTRAINTES :
- PyQt5 thread safety
- UI responsiveness
- Memory management
- Error isolation
- Performance optimization

FORMAT DE SORTIE :
1. Threading architecture
2. Worker thread implementation
3. Thread pools
4. Event-driven patterns
5. Resource management
6. Error handling
7. Performance optimization

QUALITÉ : Threading sûr, performant, maintenable
```

**Exemple de threading avancé :**
```python
# src/core/threading/video_processing_thread.py
from PyQt5.QtCore import QThread, pyqtSignal, QMutex, QWaitCondition
from PyQt5.QtGui import QImage, QPixmap
import cv2
import numpy as np
import time
import logging
from typing import Optional, Callable
from queue import Queue, Empty

class VideoProcessingThread(QThread):
    """Advanced video processing thread with queue management"""

    # Signals
    frame_ready = pyqtSignal(QPixmap, float)  # pixmap, timestamp
    processing_started = pyqtSignal()
    processing_stopped = pyqtSignal()
    error_occurred = pyqtSignal(str)  # error message
    progress_updated = pyqtSignal(int, str)  # progress percentage, status

    def __init__(self):
        super().__init__()
        self.is_running = False
        self.is_paused = False
        self.mutex = QMutex()
        self.pause_condition = QWaitCondition()

        # Processing queue
        self.processing_queue = Queue(maxsize=100)
        self.effects_engine = None
        self.video_source = None

        # Performance monitoring
        self.frame_count = 0
        self.start_time = 0
        self.fps = 0

        # Quality settings
        self.output_quality = 85  # JPEG quality
        self.frame_skip_threshold = 0  # Skip frames if processing is slow

    def set_video_source(self, video_source: Any):
        """Set video source for processing"""
        self.mutex.lock()
        self.video_source = video_source
        self.mutex.unlock()

    def set_effects_engine(self, effects_engine: Any):
        """Set effects engine for processing"""
        self.mutex.lock()
        self.effects_engine = effects_engine
        self.mutex.unlock()

    def start_processing(self):
        """Start video processing"""
        self.mutex.lock()
        if not self.is_running:
            self.is_running = True
            self.is_paused = False
            self.frame_count = 0
            self.start_time = time.time()
            self.start()
        self.mutex.unlock()

    def stop_processing(self):
        """Stop video processing"""
        self.mutex.lock()
        self.is_running = False
        self.mutex.unlock()
        self.pause_condition.wakeAll()

    def pause_processing(self):
        """Pause video processing"""
        self.mutex.lock()
        self.is_paused = True
        self.mutex.unlock()

    def resume_processing(self):
        """Resume video processing"""
        self.mutex.lock()
        self.is_paused = False
        self.mutex.unlock()
        self.pause_condition.wakeAll()

    def run(self):
        """Main processing loop"""
        logging.info("Video processing thread started")

        try:
            self.processing_started.emit()

            while self.is_running:
                self.mutex.lock()
                if self.is_paused:
                    self.mutex.unlock()
                    self.pause_condition.wait(self.mutex)
                    continue
                self.mutex.unlock()

                # Check if we have a video source
                if not self.video_source:
                    time.sleep(0.01)
                    continue

                # Read frame from video source
                ret, frame = self.video_source.read()

                if not ret:
                    logging.warning("No frame received from video source")
                    time.sleep(0.01)
                    continue

                # Process frame
                processed_frame = self.process_frame(frame)

                # Convert to QPixmap for UI
                pixmap = self.frame_to_pixmap(processed_frame)

                # Emit processed frame
                timestamp = time.time()
                self.frame_ready.emit(pixmap, timestamp)

                # Update progress
                self.frame_count += 1
                if self.frame_count % 30 == 0:  # Update every 30 frames
                    elapsed = time.time() - self.start_time
                    if elapsed > 0:
                        self.fps = self.frame_count / elapsed

                    self.progress_updated.emit(
                        int((self.frame_count % 3000) / 30),  # Progress based on frames processed
                        f"FPS: {self.fps".1f"}"
                    )

                # Small delay to maintain frame rate
                time.sleep(0.016)  # ~60 FPS

        except Exception as e:
            logging.error(f"Error in video processing thread: {e}")
            self.error_occurred.emit(str(e))
        finally:
            self.processing_stopped.emit()
            logging.info("Video processing thread stopped")

    def process_frame(self, frame: np.ndarray) -> np.ndarray:
        """Process a single video frame"""
        if self.effects_engine:
            timestamp = time.time()
            return self.effects_engine.apply_effects(frame, timestamp)
        return frame

    def frame_to_pixmap(self, frame: np.ndarray) -> QPixmap:
        """Convert numpy array to QPixmap"""
        try:
            # Convert BGR to RGB
            rgb_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

            # Create QImage
            height, width, channels = rgb_frame.shape
            bytes_per_line = channels * width

            q_image = QImage(
                rgb_frame.data,
                width,
                height,
                bytes_per_line,
                QImage.Format_RGB888
            )

            # Convert to QPixmap
            pixmap = QPixmap.fromImage(q_image)

            # Scale if too large for UI performance
            if pixmap.width() > 1920 or pixmap.height() > 1080:
                pixmap = pixmap.scaled(
                    1920, 1080,
                    Qt.KeepAspectRatio,
                    Qt.SmoothTransformation
                )

            return pixmap

        except Exception as e:
            logging.error(f"Error converting frame to pixmap: {e}")
            # Return empty pixmap as fallback
            return QPixmap(640, 480)

# src/core/threading/thread_pool_manager.py
class ThreadPoolManager:
    """Manages a pool of worker threads for various tasks"""

    def __init__(self, max_workers: int = 4):
        self.max_workers = max_workers
        self.active_threads: List[QThread] = []
        self.task_queue: Queue = Queue()
        self.is_shutdown = False
        self.mutex = QMutex()

    def submit_task(self, task: Callable, *args, **kwargs) -> 'TaskFuture':
        """Submit a task to the thread pool"""
        if self.is_shutdown:
            raise RuntimeError("Thread pool is shut down")

        future = TaskFuture(task, args, kwargs)

        self.mutex.lock()
        self.task_queue.put(future)
        self.mutex.unlock()

        # Start worker threads if needed
        self._ensure_workers()

        return future

    def _ensure_workers(self):
        """Ensure we have enough worker threads"""
        self.mutex.lock()

        while len(self.active_threads) < self.max_workers:
            worker = WorkerThread(self.task_queue)
            worker.finished.connect(lambda: self._on_worker_finished(worker))
            self.active_threads.append(worker)
            worker.start()

        self.mutex.unlock()

    def _on_worker_finished(self, worker: 'WorkerThread'):
        """Handle worker thread completion"""
        self.mutex.lock()
        if worker in self.active_threads:
            self.active_threads.remove(worker)
        self.mutex.unlock()

    def shutdown(self, wait: bool = True):
        """Shutdown the thread pool"""
        self.mutex.lock()
        self.is_shutdown = True

        # Clear task queue
        while not self.task_queue.empty():
            try:
                self.task_queue.get_nowait()
            except Empty:
                break

        self.mutex.unlock()

        # Stop all worker threads
        for worker in self.active_threads:
            worker.stop()

        if wait:
            for worker in self.active_threads:
                worker.wait()

        self.active_threads.clear()

class WorkerThread(QThread):
    """Worker thread for executing tasks"""

    finished = pyqtSignal()

    def __init__(self, task_queue: Queue):
        super().__init__()
        self.task_queue = task_queue
        self.is_running = True
        self.mutex = QMutex()
        self.wait_condition = QWaitCondition()

    def run(self):
        """Main worker loop"""
        while self.is_running:
            try:
                # Get task from queue (with timeout)
                future = self.task_queue.get(timeout=1)

                if future:
                    try:
                        # Execute task
                        result = future.execute()

                        # Set result
                        future.set_result(result)

                    except Exception as e:
                        future.set_exception(e)

                    finally:
                        # Mark task as done
                        self.task_queue.task_done()

            except Empty:
                # No tasks available, wait a bit
                self.mutex.lock()
                if self.is_running:
                    self.wait_condition.wait(self.mutex, 100)  # 100ms timeout
                self.mutex.unlock()
            except Exception as e:
                logging.error(f"Error in worker thread: {e}")

    def stop(self):
        """Stop the worker thread"""
        self.mutex.lock()
        self.is_running = False
        self.mutex.unlock()
        self.wait_condition.wakeAll()

class TaskFuture:
    """Future object for tracking task execution"""

    def __init__(self, task: Callable, args: tuple, kwargs: dict):
        self.task = task
        self.args = args
        self.kwargs = kwargs
        self.result = None
        self.exception = None
        self.is_completed = False
        self.mutex = QMutex()
        self.wait_condition = QWaitCondition()

    def execute(self) -> Any:
        """Execute the task"""
        return self.task(*self.args, **self.kwargs)

    def set_result(self, result: Any):
        """Set the task result"""
        self.mutex.lock()
        self.result = result
        self.is_completed = True
        self.mutex.unlock()
        self.wait_condition.wakeAll()

    def set_exception(self, exception: Exception):
        """Set the task exception"""
        self.mutex.lock()
        self.exception = exception
        self.is_completed = True
        self.mutex.unlock()
        self.wait_condition.wakeAll()

    def get_result(self, timeout: Optional[float] = None) -> Any:
        """Get the task result, waiting if necessary"""
        if timeout is None:
            # Wait indefinitely
            self.mutex.lock()
            while not self.is_completed:
                self.wait_condition.wait(self.mutex)
            self.mutex.unlock()
        else:
            # Wait with timeout
            import time
            start_time = time.time()

            self.mutex.lock()
            while not self.is_completed:
                remaining = timeout - (time.time() - start_time)
                if remaining <= 0:
                    self.mutex.unlock()
                    raise TimeoutError("Task execution timed out")

                self.wait_condition.wait(self.mutex, int(remaining * 1000))
            self.mutex.unlock()

        if self.exception:
            raise self.exception

        return self.result

    def is_done(self) -> bool:
        """Check if task is completed"""
        self.mutex.lock()
        result = self.is_completed
        self.mutex.unlock()
        return result
```

## 📋 Templates PyQt5

### 🖥️ Template PyQt5 Architecture
```
Tu es un expert PyQt5 et desktop applications.

TA TÂCHE : Concevoir architecture PyQt5 pour [APPLICATION]

ARCHITECTURE :
- MVC pattern
- Signal-slot system
- Custom widgets
- Layout management
- Threading integration

CONTRAINTES :
- PyQt5 best practices
- Cross-platform
- Performance optimization
- Memory management

FORMAT :
1. Architecture design
2. MVC implementation
3. Signal-slot patterns
4. Custom widgets
5. Layout management
6. Threading integration
7. Performance optimization

QUALITÉ : Architecture PyQt5 moderne, performante
```

### 🎬 Template Video Effects
```
Tu es un expert PyQt5 video processing et effects.

TA TÂCHE : Implémenter effets vidéo pour [APPLICATION]

EFFETS :
- Real-time processing
- Effect chaining
- GPU acceleration
- Performance optimization

CONTRAINTES :
- Real-time performance
- Memory management
- Threading safety
- Quality preservation

FORMAT :
1. Effects architecture
2. Real-time processing
3. Effect implementation
4. Performance optimization
5. Memory management
6. Export system
7. Testing strategies

QUALITÉ : Effets vidéo performants, qualitatifs
```

### 🧵 Template Threading
```
Tu es un expert PyQt5 threading et concurrent programming.

TA TÂCHE : Implémenter threading pour [APPLICATION]

THREADING :
- Worker threads
- Thread pools
- Event-driven patterns
- Resource management
- Error handling

CONTRAINTES :
- Thread safety
- UI responsiveness
- Memory management
- Performance optimization

FORMAT :
1. Threading architecture
2. Worker implementation
3. Thread pools
4. Event-driven patterns
5. Resource management
6. Error handling
7. Performance optimization

QUALITÉ : Threading sûr, performant, maintenable
```

## 🎯 Points clés à retenir

1. **PyQt5 Architecture** : MVC, signal-slot, threading, performance
2. **Video Effects** : Real-time processing, GPU acceleration, composability
3. **Threading** : Thread safety, UI responsiveness, resource management
4. **Memory Management** : Cleanup, optimization, leak prevention
5. **Performance** : Real-time processing, optimization, monitoring
6. **Cross-platform** : Compatibility, responsive design, native feel

## 🚀 Prochain chapitre

Maintenant que vous maîtrisez PyQt5, découvrons l'intégration complète avec des workflows de prompt pour votre stack personnalisé.

---

## 📋 Checklist qualité

- ✅ PyQt5 architecture design
- ✅ Video effects implementation
- ✅ Advanced threading patterns
- ✅ Performance optimization
- ✅ Memory management
- ✅ Cross-platform compatibility

## 🎯 Test de compréhension

**Question :** Pourquoi le pattern signal-slot de PyQt5 est-il important pour les applications desktop ?

**Réponse attendue :** Le pattern signal-slot permet une communication découplée entre les composants, facilite le threading (thread-safe), supporte les événements asynchrones, et améliore la maintenabilité en évitant les dépendances directes entre les classes.

---

**Temps de lecture estimé : 155 minutes**
**Niveau : Expert**
**Prérequis : Partie I + II + III + IV + V + VI + VII + Chapitres 1, 2**

---

🎉 **Félicitations !** Vous avez terminé la Partie VIII - Ton Stack Personnalisé.

**Prochaines étapes :** Partie IX - Annexe : Recettes Premium

---

## 📊 Synthèse des acquis

### ✅ Compétences acquises :
- PHP MVC architecture et clean code
- React components et advanced hooks
- State management patterns
- Accessibility WCAG 2.1
- PyQt5 desktop applications
- Video effects et real-time processing
- Advanced threading et concurrency
- Performance optimization

### 🎯 Niveau atteint :
- **Full-Stack Development** : Expert
- **Desktop Applications** : Expert
- **Real-time Processing** : Expert
- **UI/UX Design** : Expert

### 🚀 Prêt pour :
- Advanced system architecture
- Real-time applications
- Cross-platform development
- Performance-critical applications
- Technical leadership
- Product development
