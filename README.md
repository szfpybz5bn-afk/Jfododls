requests
import os, time, requests, urllib.parse

STYLE = ("2D flat vector illustration, thick black outline, stick-figure character with round joints, "
"plain white round face with simple stylized eyes only, flat vivid colors, no realistic shading, "
"colorful illustrated background (never pure white), centered composition, high quality, no text in image. ")

CHAR_MAP = {
    "Vlad": "Vlad the Impaler stick-figure character: black armor with red details, long dark red cape, simple dark and gold crown. ",
    "Iván": "Ivan the Terrible stick-figure character: tall dark fur hat, black and gold robe with red embroidery, stylized beard, gold scepter. ",
    "Saddam": "Saddam Hussein stick-figure character: military beret, olive green uniform, black mustache, gold medals. ",
    "Amin": "Idi Amin stick-figure character: military uniform covered in gold medals, peaked military cap, imposing pose. ",
    "Pol Pot": "Pol Pot stick-figure character: simple gray/black uniform, red and white checkered kramá scarf, simple military cap. ",
}

def build_prompt(text):
    extra = ""
    for name, desc in CHAR_MAP.items():
        if name in text:
            extra += desc
    return STYLE + extra + text

prompts = [
("0-34","Fondo: proyector de cine antiguo apagándose, pantalla en blanco con la palabra tachada 'FICCIÓN', ambiente oscuro, sugiere que es real y no ficción."),
("0-39","Fondo: calendario/reloj antiguo desgastado en penumbra, iluminación tenue de vela."),
("0-45","Fondo: escalera descendente hacia la oscuridad con 5 escalones numerados del 5 al 1, niebla en el fondo."),
("0-52","Vlad de pie frente a su castillo de piedra en Valaquia, cielo nocturno, antorchas encendidas a los lados, niebla baja."),
("0-57","Vlad sentado en un trono de piedra dentro del castillo, estandartes rojos y negros colgando, luz de antorchas."),
("1-02","Vlad de pie sosteniendo una estaca de madera, fondo de bosque oscuro con siluetas de árboles retorcidos."),
("1-07","Fondo: campo abierto de noche con hileras de estacas de madera clavadas en el suelo, niebla espesa, luna llena."),
("1-12","Vlad observando desde lo alto de una colina el campo de estacas a lo lejos, cielo gris oscuro."),
("1-17","Vlad de espaldas caminando hacia su castillo, fondo de camino de tierra entre árboles secos, atardecer rojizo."),
("1-22","Ejército enemigo estilizado deteniéndose asustado frente al campo de estacas, niebla y cielo plomizo."),
("1-26","Fondo: horizonte con cientos de siluetas de estacas diminutas hasta donde alcanza la vista, cielo nublado."),
("1-32","Vlad de pie con los brazos cruzados frente a su castillo iluminado por antorchas, expresión firme."),
("1-37","Fondo: mapa estilizado de Valaquia con un ícono de miedo/ojo vigilante flotando sobre el territorio."),
("1-42","Vlad sentado en su trono, mensajeros stickman arrodillados frente a él, sala de piedra con antorchas."),
("1-47","Aldeanos stickman en un pueblo mirando hacia el castillo a lo lejos con temor, cielo nublado."),
("1-52","Fondo: silueta de un castillo con ligero toque gótico, niebla, luna llena."),
("1-58","Murciélagos estilizados volando frente a la luna sobre el castillo de Vlad, cielo nocturno oscuro."),
("2-03","Vlad de pie mirando al horizonte desde una torre del castillo, cielo estrellado oscuro."),
("2-09","Iván niño sentado en un trono grande dentro del Kremlin, columnas doradas, ventanas con nieve."),
("2-14","Iván adulto de pie en la sala del trono, luz fría entrando por ventanales, nieve cayendo afuera."),
("2-20","Fondo: estandarte con el símbolo de la Oprichnina (perro y escoba estilizados en negro), sala oscura de piedra."),
("2-25","Grupo de nobles stickman arrodillados temerosos frente al trono de Iván, salón de piedra oscuro."),
("2-30","Iván señalando con el dedo hacia una lista/pergamino, expresión seria, fondo de columnas de piedra."),
("2-36","Fondo: pergamino con nombres tachados en rojo, iluminado por una vela, mesa de madera oscura."),
("2-41","Iván caminando solo por un pasillo largo de piedra del Kremlin, sombras alargadas."),
("2-47","Iván y su hijo discutiendo frente a frente en una sala del palacio, tensión, luz de velas."),
("2-53","Fondo: sala del palacio en silencio, una vela apagándose lentamente, tono sombrío."),
("2-57","Historiadores stickman con libros antiguos debatiendo alrededor de una mesa, biblioteca con estanterías oscuras."),
("3-02","Iván sentado solo en su trono con la cabeza baja, salón vacío y oscuro, ventanales con nieve."),
("3-06","Fondo: corona pequeña caída en el suelo de piedra, luz tenue de vela."),
("3-12","Iván de pie frente a una tumba/lápida estilizada dentro de una capilla ortodoxa, velas encendidas."),
("3-17","Fondo: árbol genealógico dorado con una rama rota/apagada, sobre fondo oscuro de palacio."),
("3-22","Iván solo en el trono vacío del imperio, salón inmenso y oscuro, sensación de vacío de poder."),
("3-27","Saddam de pie frente a un palacio presidencial con columnas, bandera de Irak ondeando, cielo desértico."),
("3-32","Saddam en una sala de gobierno con mapa de Irak en la pared, postura firme con brazos cruzados."),
("3-39","Fondo: edificios gubernamentales iraquíes estilizados con soldados stickman en la entrada, cielo cálido."),
("3-44","Saddam caminando entre filas de soldados stickman en un patio militar, banderas verdes ondeando."),
("3-49","Fondo: puerta de una celda/prisión estilizada, pasillo oscuro con una sola luz colgante."),
("3-54","Saddam sentado en un despacho con documentos sobre el escritorio, luz tenue, expresión seria."),
("4-01","Fondo: sala grande de reuniones del partido Baaz, filas de sillas ocupadas por siluetas stickman, atril al frente."),
("4-07","Saddam de pie en el atril leyendo un papel frente a la sala llena de miembros del partido."),
("4-14","Hombres stickman siendo escoltados fuera de la sala por guardias, ambiente tenso, luz fría."),
("4-18","Fondo: puerta cerrándose tras las siluetas escoltadas, pasillo oscuro."),
("4-23","Miembros del partido stickman sentados en silencio observando, expresión de temor, sala iluminada fríamente."),
("4-29","Saddam de pie con autoridad frente a la sala, mano en el pecho, luz dramática desde arriba."),
("4-35","Fondo: mapa de Irak con un ícono de ojo vigilante sobre distintas regiones."),
("4-40","Fondo: colinas y aldeas kurdas estilizadas en tono gris, cielo nublado, ambiente sombrío."),
("4-45","Fondo: mapa de la región norte de Irak con un símbolo de humo estilizado a la distancia, tono grisáceo."),
("4-50","Fondo: pueblo estilizado vacío y silencioso, casas simples, cielo gris oscuro."),
("4-57","Fondo: cielo con una nube de humo estilizada sobre el horizonte de un pueblo, tono sombrío."),
("5-02","Saddam de pie de espaldas mirando un mapa de Irak en la pared de su despacho, luz fría."),
("5-10","Idi Amin de pie frente a un palacio presidencial con palmeras, cielo cálido africano."),
("5-15","Amin en un desfile militar, soldados stickman en filas, banderas de Uganda, sabana de fondo."),
("5-23","Fondo: calendario estilizado 1971-1979 sobre un mapa de Uganda, tono cálido pero sombrío."),
("5-28","Grupo variado de stickmen de pie en una plaza, expresión de incertidumbre."),
("5-33","Fondo: familia stickman esperando frente a una puerta, luz tenue de atardecer, ambiente de espera angustiosa."),
("5-38","Fondo: silla vacía en una casa sencilla africana, ventana con luz de atardecer entrando, ambiente melancólico."),
("5-43","Amin de pie dando un discurso desde un balcón del palacio, multitud de siluetas abajo."),
("5-47","Fondo: fila de personas stickman con maletas caminando hacia la salida de un puerto/aeropuerto estilizado."),
("5-53","Amin posando con lentes de sol y uniforme cargado de medallas frente a cámaras estilizadas, palacio de fondo."),
("5-59","Amin de pie con pose imponente y sonrisa amplia, multitud aplaudiendo alrededor, sabana con palmeras."),
("6-04","Fondo: pasillo oscuro del palacio presidencial, puertas cerradas, iluminación tenue y fría."),
("6-09","Fondo: sala privada del palacio en penumbra, ambiente misterioso."),
("6-14","Fondo: periódicos y rumores estilizados flotando en penumbra, tono ambiguo."),
("6-19","Fondo: signo de interrogación grande y estilizado sobre un mapa de Uganda en penumbra."),
("6-23","Fondo: mapa de Uganda con puntos oscuros dispersos representando desapariciones, tono grisáceo."),
("6-28","Fondo: calle de un pueblo ugandés vacía y silenciosa al atardecer."),
("6-34","Familia stickman ugandesa mirando al horizonte con preocupación, sabana y cielo anaranjado de atardecer."),
("6-40","Fondo: palacio presidencial derrumbándose/oscureciéndose, cielo nublado, fin de una era."),
("6-46","Fondo: bandera de Uganda ondeando sobre un país estilizado marcado por sombras."),
("6-52","Pol Pot de pie frente a un mapa estilizado de Camboya, expresión fría, fondo oscuro."),
("6-58","Fondo: casa sencilla camboyana con una puerta cerrada y un candado, cielo gris."),
("7-03","Fondo: ciudad de Phnom Penh estilizada quedándose vacía, calles desiertas, cielo grisáceo."),
("7-12","Soldados del Jemer Rojo stickman entrando a Phnom Penh, banderas rojas."),
("7-16","Fondo: fila larga de personas stickman caminando con bultos hacia el campo, camino de tierra, cielo gris."),
("7-23","Fondo: campos de arroz extensos con trabajadores stickman en jornada agotadora, sol intenso."),
("7-27","Fondo: campamento rural sencillo con tiendas de paja, ambiente de escasez, cielo nublado."),
("7-33","Fondo: cartel estilizado con un signo de sospecha sobre una figura genérica stickman."),
("7-39","Persona stickman con un libro siendo señalada por un soldado del Jemer Rojo, campo de fondo."),
("7-45","Persona stickman con gafas siendo señalada, campamento de fondo."),
("7-49","Persona stickman con ropa antigua/formal siendo señalada por soldados, fondo rural gris."),
("7-55","Fondo: exterior de un edificio tipo escuela convertido en prisión, rejas en las ventanas, cielo gris."),
("8-00","Fondo: pasillo largo del edificio-prisión con puertas numeradas, iluminación fría."),
("8-06","Fondo: fachada de Tuol Sleng, cerco de alambre, cielo nublado."),
("8-10","Fondo: sala de interrogatorio sencilla y vacía dentro del edificio, silla y mesa, luz fría."),
("8-15","Fondo: pila de papeles/confesiones estilizados sobre un escritorio, luz tenue."),
("8-20","Fondo: camino rural saliendo de la ciudad hacia el campo abierto, cielo gris, atardecer."),
("8-25","Fondo: campo abierto con árboles dispersos y monumentos conmemorativos estilizados, tono solemne."),
("8-33","Fondo: mapa de Camboya con una gran porción sombreada en gris oscuro."),
("8-38","Fondo: gráfico simple estilizado tipo balanza entre población y víctimas, tono sobrio."),
("8-44","Fondo: calendario estilizado 1975-1979 sobre campos de arroz, cielo gris."),
("8-49","Fondo: campo de batalla vacío y silencioso, sin soldados, tono solemne."),
("8-54","Fondo: mapa de Camboya con silueta del país oscurecida casi por completo."),
("9-00","Pol Pot de pie a la distancia observando el campamento, sin intervenir directamente, campo de arroz de fondo."),
("9-04","Fondo: engranajes/sistema estilizado funcionando solo, sin manos visibles, tono frío y mecánico."),
("9-10","Fondo oscuro con los cinco stickman de pie en fila, iluminados individualmente, niebla baja."),
("9-15","Vlad iluminado con un ícono de miedo (ojo) sobre su cabeza, los otros cuatro en sombra detrás."),
("9-22","Iván iluminado con un ícono de paranoia (espiral) sobre su cabeza, resto en sombra."),
("9-26","Saddam iluminado con un ícono de estado/edificio sobre su cabeza, resto en sombra."),
("9-31","Idi Amin iluminado con un ícono de terror (grito estilizado) sobre su cabeza, resto en sombra."),
("9-36","Pol Pot iluminado con un ícono de engranaje/sistema sobre su cabeza, resto en sombra."),
("9-41","Los cinco stickman completos en fila, fondo oscuro con niebla, luz fría general sobre todos."),
("9-45","Fondo: signo de interrogación gigante flotando sobre las cinco siluetas, tono inquietante."),
("9-51","Fondo: botón de suscripción estilizado apareciendo en penumbra, las cinco siluetas difuminándose al fondo."),
]

os.makedirs("fotos", exist_ok=True)

for i, (pid, text) in enumerate(prompts, start=1):
    prompt = build_prompt(text)
    encoded = urllib.parse.quote(prompt)
    url = f"https://image.pollinations.ai/prompt/{encoded}?width=1024&height=1024&nologo=true"
    out_path = f"fotos/{i:03d}_{pid}.png"
    for attempt in range(3):
        try:
            r = requests.get(url, timeout=120)
            if r.status_code == 200 and len(r.content) > 1000:
                with open(out_path, "wb") as f:
                    f.write(r.content)
                print(f"{i}/{len(prompts)} OK -> {out_path}")
                break
            else:
                raise Exception(f"status {r.status_code}")
        except Exception as e:
            print(f"{i}/{len(prompts)} intento {attempt+1} fallo: {e}")
            time.sleep(5)
    time.sleep(1)

print("Listo, todas las imagenes procesadas.")
