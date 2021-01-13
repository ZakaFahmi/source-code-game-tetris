Penjelasan/ Analisa tetris.py
BoardWidth = 10
BoardHeight = 22
Speed = 300

Ini adalah variabel kelas Board. BoardWidth dan BoardHeight menentukan ukuran papan
dalam blok. Speed menentukan kecepatan game. Setiap 300 ms siklus permainan baru akan
dimulai.

def pause(self):
if not self.isStarted:
return
self.isPaused = not self.isPaused
if self.isPaused:
self.timer.stop()self.msg2Statusbar.emit("paused")
else:
self.timer.start(Board.Speed, self)
self.msg2Statusbar.emit(str(self.numLinesRemoved))
self.update()


Metode jeda menghentikan permainan. Ini menghentikan timer dan menampilkan pesan di
bilah status.


def timerEvent(self, event):
if event.timerId() == self.timer.timerId():
if self.isWaitingAfterLine:
self.isWaitingAfterLine = False
self.newPiece()
else:
self.oneLineDown()
else:
super(Board, self).timerEvent(event)


Dalam acara pengatur waktu, saya membuat bagian baru setelah yang sebelumnya
dijatuhkan ke bawah atau kami memindahkan bagian yang jatuh satu baris ke bawah.
def clearBoard(self):
for i in range(Board.BoardHeight * Board.BoardWidth):
self.board.append(Tetrominoe.NoShape)
Metode clearBoard membersihkan papan dengan mengatur Tetrominoe.NoShape di
setiap blok papan.def removeFullLines(self):


numFullLines = 0
rowsToRemove = []
for i in range(Board.BoardHeight):
n=0
for j in range(Board.BoardWidth):
if not self.shapeAt(j, i) == Tetrominoe.NoShape:
n=n+1
if n == 10:
rowsToRemove.append(i)
rowsToRemove.reverse()
for m in rowsToRemove:
for k in range(m, Board.BoardHeight):
for l in range(Board.BoardWidth):
self.setShapeAt(l, k, self.shapeAt(l, k + 1))
numFullLines = numFullLines + len(rowsToRemove)


Jika potongan menyentuh bagian bawah, saya memanggil metode removeFullLines.saya
menemukan semua baris penuh dan menghapusnya. saya melakukannya dengan
memindahkan semua garis di atas garis penuh saat ini untuk dihapus satu baris ke bawah.
Perhatikan bahwa saya membalik urutan garis yang akan dihapus. Jika tidak, itu tidak akan
bekerja dengan benar. Dalam kasus saya, saya menggunakan gravitasi naif. Ini berarti bahwa
bidak mungkin mengambang di atas celah kosong.
def tryMove(self, newPiece, newX, newY):
for i in range(4):
x = newX + newPiece.x(i)
y = newY - newPiece.y(i)
if x < 0 or x >= Board.BoardWidth or y < 0 or y >= Board.BoardHeight:return False
if self.shapeAt(x, y) != Tetrominoe.NoShape:
return False
self.curPiece = newPiece
self.curX = newX
self.curY = newY
self.update()
return True
Dalam metode tryMove saya mencoba untuk memindahkan bentuk . Jika bentuknya berada di
tepi papan atau berdekatan dengan bagian lain, saya mengembalikan False. Jika tidak, saya
menempatkan potongan jatuh saat ini ke posisi baru.


class Shape(object):
coordsTable = (
((0, 0),
(0, 0),
((0, -1), (0, 0),
(0, 0),
(0, 0)),
(-1, 0), (-1, 1)),
)


Tupel coordsTable menampung semua kemungkinan nilai koordinat dari potongan tetris
kami. Ini adalah template dari mana semua bagian mengambil nilai koordinatnya


def rotateLeft(self):
if self.pieceShape == Tetrominoe.SquareShape:
return self
result = Shape()
result.pieceShape = self.pieceShape
for i in range(4):
result.setX(i, self.y(i))
result.setY(i, -self.x(i))
return result


Metode rotateLeft memutar bagian ke kiri. Persegi tidak harus diputar. Itulah mengapa
saya hanya mengembalikan referensi ke objek saat ini. Potongan baru dibuat dan
koordinatnya disetel ke bagian yang diputar.
