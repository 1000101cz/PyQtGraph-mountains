import numpy as np
from PyQt5.QtWidgets import QApplication
from PyQt5 import QtCore
import pyqtgraph as pg
import pyqtgraph.opengl as gl
import sys
from opensimplex import OpenSimplex

pg.setConfigOption('useNumba', True)
pg.setConfigOption('useCupy', True)
pg.setConfigOption('useOpenGL', True)
pg.setConfigOption('enableExperimental', True)
# pg.setConfigOption('antialias', True)


class CustomGLViewWidget(gl.GLViewWidget):
    def __init__(self, *args, **kwargs):
        super().__init__(*args, **kwargs)

    def mousePressEvent(self, ev):
        ...

    def mouseMoveEvent(self, ev):
        ...

    def mouseReleaseEvent(self, ev):
        ...


class LoadingWindow:
    def __init__(self):
        self.app = QApplication(sys.argv)
        self.w = CustomGLViewWidget()
        self.w.setGeometry(0, 100, 1280, 720)
        self.w.show()
        self.w.setWindowTitle('Loading...')
        self.w.setCameraPosition(distance=20, elevation=7)

        # constants and arrays
        self.y_points = range(-20, 20)
        self.x_points = range(-20, 20)
        self.n_faces = len(self.y_points)
        self.offset = 0

        # perlin noise object
        self.tmp = OpenSimplex(69)

        # create the vertices array
        vertices = np.array([
            [x,
             y,
             7.5 * self.tmp.noise2(x=n / 5, y=m / 5)
             ] for n, x in enumerate(self.x_points) for m, y in enumerate(self.y_points)
        ])

        # create the faces and colors arrays
        faces = []
        colors = []
        for m in range(self.n_faces - 1):
            y_off = m * self.n_faces
            for n in range(self.n_faces - 1):
                faces.append([n + y_off, y_off + n + self.n_faces, y_off + n + self.n_faces + 1])
                faces.append([n + y_off, y_off + n + 1, y_off + n + self.n_faces + 1])
                colors.append([0, 0, 0, 0])
                colors.append([0, 0, 0, 0])

        faces = np.array(faces)
        colors = np.array(colors)

        # create the mesh item
        self.m1 = gl.GLMeshItem(
            vertexes=vertices,
            faces=faces, faceColors=colors,
            smooth=False, drawEdges=True,
        )
        self.m1.setGLOptions('additive')
        self.w.addItem(self.m1)

    def update(self):
        vertices = np.array([
            [x,
             y,
             1.4 * self.tmp.noise2(x=n / 5 + self.offset, y=m / 5 + self.offset)
             ] for n, x in enumerate(self.x_points) for m, y in enumerate(self.y_points)
        ])

        faces = []
        colors = []
        for m in range(self.n_faces - 1):
            y_off = m * self.n_faces
            for n in range(self.n_faces - 1):
                faces.append([n + y_off, y_off + n + self.n_faces, y_off + n + self.n_faces + 1])
                faces.append([n + y_off, y_off + n + 1, y_off + n + self.n_faces + 1])
                colors.append([n / self.n_faces, 1 - n / self.n_faces, m / self.n_faces, float('inf')])
                colors.append([n / self.n_faces, 1 - n / self.n_faces, m / self.n_faces, float('inf')])

        faces = np.array(faces, dtype=np.uint32)
        colors = np.array(colors, dtype=np.float32)

        self.m1.setMeshData(vertexes=vertices, faces=faces, faceColors=colors)

        self.offset -= 0.005

    @staticmethod
    def start():
        QApplication.instance().exec_()

    def animation(self):
        timer = QtCore.QTimer()
        timer.timeout.connect(self.update)
        timer.start(10)
        self.start()
        self.update()


if __name__ == '__main__':
    t = LoadingWindow()
    t.animation()
