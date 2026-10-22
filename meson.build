project(
  'SFML-custom',
  'cpp',
  version : '3.2.0',
  meson_version : '>=1.2.0',
  default_options : [
    'cpp_std=c++17',
    'warning_level=2',
    'b_lundef=false',
    'default_library=shared',
  ],
)

# ---------------------------------------------------------------------------
# Meson mirrors the original CMake metadata used by SFML.
# The build is deliberately organised in a single root entry point so that it is
# easy to consume from desktop builds on Windows, macOS and Linux without having
# to recurse through multiple CMakeLists.txt files.
# ---------------------------------------------------------------------------

host_system = host_machine.system()
is_windows = host_system == 'windows'
is_linux = host_system == 'linux'
is_macos = host_system == 'darwin'
is_freebsd = host_system == 'freebsd'
is_netbsd = host_system == 'netbsd'
is_openbsd = host_system == 'openbsd'
is_bsd = is_freebsd or is_netbsd or is_openbsd

# Default module selection follows the original project conventions: all core
# modules are enabled by default and examples are built unless the user opts out.
# The Graphics module implicitly forces the Window module to be enabled because
# the CMake version does this for compatibility with the SFML dependency graph.
build_window = get_option('sfml_build_window') or get_option('sfml_build_graphics')
build_graphics = get_option('sfml_build_graphics')
build_audio = get_option('sfml_build_audio')
build_network = get_option('sfml_build_network')
build_examples = get_option('sfml_build_examples')

if build_graphics and not get_option('sfml_build_window')
  warning('Graphics requires Window; enabling the Window module automatically.')
endif

# Common include directories used by the library sources.
root_include = include_directories('include')
extlibs_include = include_directories(
  'extlibs/headers',
  'extlibs/headers/cpp-unicodelib',
  'extlibs/headers/glad/include',
  'extlibs/headers/vulkan',
)

threads_dep = dependency('threads')

# ---------------------------------------------------------------------------
# sfml-system
# ---------------------------------------------------------------------------
sfml_system_sources = files(
  'src/SFML/System/Clock.cpp',
  'src/SFML/System/Err.cpp',
  'src/SFML/System/FileInputStream.cpp',
  'src/SFML/System/MemoryInputStream.cpp',
  'src/SFML/System/Sleep.cpp',
  'src/SFML/System/String.cpp',
  'src/SFML/System/TimeoutWithPredicate.cpp',
  'src/SFML/System/Utils.cpp',
  'src/SFML/System/Version.cpp',
)

if is_windows
  sfml_system_sources += files('src/SFML/System/Win32/SleepImpl.cpp')
else
  sfml_system_sources += files('src/SFML/System/Unix/SleepImpl.cpp')
endif

sfml_system = library(
  'sfml-system',
  sfml_system_sources,
  include_directories : [root_include, extlibs_include],
  dependencies : [threads_dep],
  install : true,
)

alias_target('sfml_system', sfml_system)

# ---------------------------------------------------------------------------
# sfml-window
# ---------------------------------------------------------------------------
window_sources = files(
  'src/SFML/Window/Clipboard.cpp',
  'src/SFML/Window/Context.cpp',
  'src/SFML/Window/Cursor.cpp',
  'src/SFML/Window/GlContext.cpp',
  'src/SFML/Window/GlResource.cpp',
  'src/SFML/Window/InputImpl.cpp',
  'src/SFML/Window/Joystick.cpp',
  'src/SFML/Window/JoystickManager.cpp',
  'src/SFML/Window/Keyboard.cpp',
  'src/SFML/Window/Mouse.cpp',
  'src/SFML/Window/Sensor.cpp',
  'src/SFML/Window/SensorManager.cpp',
  'src/SFML/Window/Touch.cpp',
  'src/SFML/Window/VideoMode.cpp',
  'src/SFML/Window/Vulkan.cpp',
  'src/SFML/Window/Window.cpp',
  'src/SFML/Window/WindowBase.cpp',
  'src/SFML/Window/WindowImpl.cpp',
)

# Platform-specific files are selected by the target OS. This keeps the Meson
# build portable across desktop environments while matching the project logic in
# the original CMake configuration.
if is_windows
  window_sources += files(
    'src/SFML/Window/Win32/ClipboardImpl.cpp',
    'src/SFML/Window/Win32/CursorImpl.cpp',
    'src/SFML/Window/Win32/InputImpl.cpp',
    'src/SFML/Window/Win32/JoystickImpl.cpp',
    'src/SFML/Window/Win32/SensorImpl.cpp',
    'src/SFML/Window/Win32/VideoModeImpl.cpp',
    'src/SFML/Window/Win32/VulkanImplWin32.cpp',
    'src/SFML/Window/Win32/WindowImplWin32.cpp',
    'src/SFML/Window/Win32/WglContext.cpp',
  )
elseif is_linux or is_bsd
  if get_option('sfml_use_drm')
    window_sources += files(
      'src/SFML/Window/DRM/ClipboardImpl.cpp',
      'src/SFML/Window/DRM/CursorImpl.cpp',
      'src/SFML/Window/DRM/InputImpl.cpp',
      'src/SFML/Window/DRM/VideoModeImpl.cpp',
      'src/SFML/Window/DRM/DRMContext.cpp',
      'src/SFML/Window/DRM/WindowImplDRM.cpp',
      'src/SFML/Window/EGLCheck.cpp',
    )
  else
    window_sources += files(
      'src/SFML/Window/Unix/ClipboardImpl.cpp',
      'src/SFML/Window/Unix/CursorImpl.cpp',
      'src/SFML/Window/Unix/InputImpl.cpp',
      'src/SFML/Window/Unix/KeyboardImpl.cpp',
      'src/SFML/Window/Unix/KeySymToKeyMapping.cpp',
      'src/SFML/Window/Unix/KeySymToUnicodeMapping.cpp',
      'src/SFML/Window/Unix/SensorImpl.cpp',
      'src/SFML/Window/Unix/Display.cpp',
      'src/SFML/Window/Unix/VideoModeImpl.cpp',
      'src/SFML/Window/Unix/VulkanImplX11.cpp',
      'src/SFML/Window/Unix/WindowImplX11.cpp',
      'src/SFML/Window/Unix/GlxContext.cpp',
      'src/SFML/Window/EGLCheck.cpp',
    )
  endif

  if is_linux
    window_sources += files('src/SFML/Window/Unix/JoystickImpl.cpp')
  elif is_freebsd
    window_sources += files('src/SFML/Window/FreeBSD/JoystickImpl.cpp')
  elif is_openbsd
    window_sources += files('src/SFML/Window/OpenBSD/JoystickImpl.cpp')
  elif is_netbsd
    window_sources += files('src/SFML/Window/NetBSD/JoystickImpl.cpp')
  endif
elseif is_macos
  window_sources += files(
    'src/SFML/Window/macOS/cg_sf_conversion.mm',
    'src/SFML/Window/macOS/CursorImpl.mm',
    'src/SFML/Window/macOS/ClipboardImpl.mm',
    'src/SFML/Window/macOS/InputImpl.mm',
    'src/SFML/Window/macOS/HIDJoystickManager.cpp',
    'src/SFML/Window/macOS/JoystickImpl.cpp',
    'src/SFML/Window/macOS/NSImage+raw.mm',
    'src/SFML/Window/macOS/SensorImpl.cpp',
    'src/SFML/Window/macOS/SFApplication.m',
    'src/SFML/Window/macOS/SFApplicationDelegate.m',
    'src/SFML/Window/macOS/SFContext.mm',
    'src/SFML/Window/macOS/SFKeyboardModifiersHelper.mm',
    'src/SFML/Window/macOS/SFOpenGLView.mm',
    'src/SFML/Window/macOS/SFOpenGLView+keyboard.mm',
    'src/SFML/Window/macOS/SFOpenGLView+mouse.mm',
    'src/SFML/Window/macOS/SFWindow.m',
    'src/SFML/Window/macOS/SFWindowController.mm',
    'src/SFML/Window/macOS/SFViewController.mm',
    'src/SFML/Window/macOS/VideoModeImpl.cpp',
    'src/SFML/Window/macOS/WindowImplCocoa.mm',
  )
endif

window_deps = [threads_dep]
if is_windows
  window_deps += cc.find_library('winmm')
  window_deps += cc.find_library('gdi32')
  window_deps += cc.find_library('opengl32')
elif is_linux or is_bsd
  if get_option('sfml_use_drm')
    window_deps += dependency('libdrm')
    window_deps += dependency('gbm')
  else
    window_deps += dependency('x11')
    window_deps += dependency('xrandr')
    window_deps += dependency('xcursor')
    window_deps += dependency('xi')
  endif
  window_deps += dependency('libudev')
  window_deps += cc.find_library('dl')
  window_deps += cc.find_library('rt')
elif is_macos
  window_deps += dependency('appleframeworks', modules : ['Foundation', 'AppKit', 'IOKit', 'Carbon'])
endif

sfml_window = library(
  'sfml-window',
  window_sources,
  include_directories : [root_include, extlibs_include],
  dependencies : window_deps,
  link_with : [sfml_system],
  install : true,
)

alias_target('sfml_window', sfml_window)

# ---------------------------------------------------------------------------
# sfml-graphics
# ---------------------------------------------------------------------------
graphics_sources = files(
  'src/SFML/Graphics/BlendMode.cpp',
  'src/SFML/Graphics/CircleShape.cpp',
  'src/SFML/Graphics/Color.cpp',
  'src/SFML/Graphics/ConvexShape.cpp',
  'src/SFML/Graphics/Font.cpp',
  'src/SFML/Graphics/FontCollection.cpp',
  'src/SFML/Graphics/Glyph.cpp',
  'src/SFML/Graphics/Image.cpp',
  'src/SFML/Graphics/PrimitiveType.cpp',
  'src/SFML/Graphics/RectangleShape.cpp',
  'src/SFML/Graphics/RenderStates.cpp',
  'src/SFML/Graphics/RenderTexture.cpp',
  'src/SFML/Graphics/RenderWindow.cpp',
  'src/SFML/Graphics/Shader.cpp',
  'src/SFML/Graphics/Shape.cpp',
  'src/SFML/Graphics/Sprite.cpp',
  'src/SFML/Graphics/Text.cpp',
  'src/SFML/Graphics/Texture.cpp',
  'src/SFML/Graphics/Transform.cpp',
  'src/SFML/Graphics/Transformable.cpp',
  'src/SFML/Graphics/Vertex.cpp',
  'src/SFML/Graphics/VertexArray.cpp',
  'src/SFML/Graphics/View.cpp',
)

graphics_deps = [dependency('freetype2'), dependency('gl')]
if is_linux or is_bsd
  graphics_deps += dependency('libpng')
endif

sfml_graphics = library(
  'sfml-graphics',
  graphics_sources,
  include_directories : [root_include, extlibs_include],
  dependencies : graphics_deps,
  link_with : [sfml_window, sfml_system],
  install : true,
)

alias_target('sfml_graphics', sfml_graphics)

# ---------------------------------------------------------------------------
# sfml-audio
# ---------------------------------------------------------------------------
audio_sources = files(
  'src/SFML/Audio/AlResource.cpp',
  'src/SFML/Audio/AudioDevice.cpp',
  'src/SFML/Audio/InputSoundFile.cpp',
  'src/SFML/Audio/Listener.cpp',
  'src/SFML/Audio/Music.cpp',
  'src/SFML/Audio/OutputSoundFile.cpp',
  'src/SFML/Audio/Sound.cpp',
  'src/SFML/Audio/SoundBuffer.cpp',
  'src/SFML/Audio/SoundBufferRecorder.cpp',
  'src/SFML/Audio/SoundFileReaderFlac.cpp',
  'src/SFML/Audio/SoundFileReaderOgg.cpp',
  'src/SFML/Audio/SoundFileReaderWav.cpp',
  'src/SFML/Audio/SoundFileWriterFlac.cpp',
  'src/SFML/Audio/SoundFileWriterOgg.cpp',
  'src/SFML/Audio/SoundFileWriterWav.cpp',
  'src/SFML/Audio/SoundRecorder.cpp',
  'src/SFML/Audio/SoundSource.cpp',
  'src/SFML/Audio/SoundStream.cpp',
)

audio_deps = [
  dependency('openal'),
  dependency('vorbis'),
  dependency('flac'),
  dependency('ogg'),
]

sfml_audio = library(
  'sfml-audio',
  audio_sources,
  include_directories : [root_include, extlibs_include],
  dependencies : audio_deps,
  link_with : [sfml_system, sfml_window],
  install : true,
)

alias_target('sfml_audio', sfml_audio)

# ---------------------------------------------------------------------------
# sfml-network
# ---------------------------------------------------------------------------
network_sources = files(
  'src/SFML/Network/FTP.cpp',
  'src/SFML/Network/Http.cpp',
  'src/SFML/Network/IpAddress.cpp',
  'src/SFML/Network/Packet.cpp',
  'src/SFML/Network/Socket.cpp',
  'src/SFML/Network/TcpListener.cpp',
  'src/SFML/Network/TcpSocket.cpp',
  'src/SFML/Network/UdpSocket.cpp',
  'src/SFML/Network/SocketSelector.cpp',
)

network_deps = [threads_dep]
if is_windows
  network_deps += cc.find_library('ws2_32')
elif is_linux or is_bsd or is_macos
  network_deps += cc.find_library('ws2_32', required : false)
endif

sfml_network = library(
  'sfml-network',
  network_sources,
  include_directories : [root_include, extlibs_include],
  dependencies : network_deps,
  link_with : [sfml_system],
  install : true,
)

alias_target('sfml_network', sfml_network)

# ---------------------------------------------------------------------------
# Examples
# ---------------------------------------------------------------------------
# The project originally builds the examples from the examples/ tree depending on
# which modules have been enabled. The build below wires up the most common GUI,
# networking and audio example programs as named executables and then exposes
# them through a single sfml-examples alias target.
example_targets = []

examples = [
  ['window', 'examples/window/Window.cpp', [sfml_window]],
  ['event-handling', 'examples/event_handling/EventHandling.cpp', [sfml_graphics, sfml_window]],
  ['opengl', 'examples/opengl/OpenGL.cpp', [sfml_graphics, sfml_window]],
  ['stencil', 'examples/stencil/Stencil.cpp', [sfml_graphics, sfml_window]],
  ['text', 'examples/text/Text.cpp', [sfml_graphics, sfml_window]],
  ['sound', 'examples/sound/Sound.cpp', [sfml_audio, sfml_graphics]],
  ['sockets', 'examples/sockets/Sockets.cpp', [sfml_network]],
  ['ftp', 'examples/ftp/Ftp.cpp', [sfml_network]],
  ['http', 'examples/http/Http.cpp', [sfml_network]],
]

foreach example : examples
  executable_name = example[0]
  source_file = example[1]
  deps = example[2]

  example_targets += executable(
    executable_name,
    source_file,
    include_directories : root_include,
    link_with : deps,
    install : false,
  )
endforeach

if build_examples
  alias_target('sfml-examples', example_targets)
endif

# ---------------------------------------------------------------------------
# Install metadata for packagers and IDEs.
# ---------------------------------------------------------------------------
# This project intentionally does not attempt to reproduce every CMake-only
# packaging detail; the Meson files focus on the build graph itself and the
# project layout used by the repository.

meson.add_install_script('sh', '-c', 'echo "SFML build configuration complete"')

# ---------------------------------------------------------------------------
# Notes
# ---------------------------------------------------------------------------
#   - Build with:   meson setup builddir
#   - Then:        meson compile -C builddir
#   - Common toggles:
#       meson configure builddir -Dsfml_build_window=true
#       meson configure builddir -Dsfml_build_graphics=true
#       meson configure builddir -Dsfml_build_audio=true
#       meson configure builddir -Dsfml_build_network=true
#       meson configure builddir -Dsfml_build_examples=true
#       meson configure builddir -Dsfml_use_drm=false
# ---------------------------------------------------------------------------
