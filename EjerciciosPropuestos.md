#EJERCICIOS PROPUESTOS ED

**Ejercicio 1. Implementar una función *int max_subtree(bintree<int>* & *T)* que devuelva la suma de etiquetas máxima de entre todos los posibles subárboles del árbol binario T.
Las etiquetas de los nodos pueden ser positivas o negativas (por tanto, el súbarbol vacío puede ser el de mayor suma, que se considera 0).**

Para resolverlo, asignaremos a cada nodo una puntuación, que será la suma de las etiquetas del subárbol que cuelga de él. Así, la puntuación de cada nodo será el valor de su etiqueta, más la puntuación de su hijo izquierda y más la puntuación de su hijo derecha (hijo no existente tiene puntuación 0).

Recorreremos, entonces, el árbol en postorden (para visitar antes a los hijos), iremos asignando puntuaciones a los nodos y nos iremos quedando con la mayor puntuación.

```
int max_subtree(bintree<int> & T){
  int maximo = 0;
  bintree<int>::postorder_iterator it;

  for(it = T.begin_postorder(); it != T.end_postorder(); ++it){
    if(!it.getNodo().left().null())
      *it.getNodo() += *it.getNodo().left();

    if(!it.getNodo().right().null())
      *it.getNodo() += *it.getNodo().right();

    if(maximo < *it.getNodo())
      maximo = *it.getNodo();
  }
  return maximo;
}
```

Ha sido necesaria la implementación de la siguiente función:
```
nodo & bintree<int>::postorder_iterator::getNodo(){return elnodo};
```
---

**Ejercicio 2. Implementar una función *void rutalista(list<int>* & *l, int n)* que traslada los n primeros elementos de la lista al final de la misma. Ejemplo: lista={1, 3, 5, 4, 2, 6}, con n=2 obtenemos lista={5, 4, 2, 6, 1, 3}.**

Con precondición $0<=$ n $<=$ l.size(), la siguiente función resuelve el problema.

```
void rutalista(list<int> & l, int N){
	list<int>::iterator it = l.begin();
	int contador = 0;

	while(contador < N){
		l.push_back(*it);
		it++;
		l.erase(l.begin());
		contador++;
	}
}
```
---

**Ejercicio 3. Implementar una función *void juntalista(list<int>* & *l, int n)* que dada una lista agrupe los elementos de n en n dejando su suma. Ejemplo: lista={1, 3, 2, 4, 5, 2, 2, 3, 5, 7, 4, 3, 2, 2}, con n=3 queda lista={6, 11, 10, 14, 4}. No pueden usarse estructuras auxiliares y la función debe ser O(n).**

Para resolver este ejercicio usaremos tres iteradores: escritura, que irá tomando el primer valor de cada subgrupo de n elementos, donde se acumulará la suma del subgrupo; lectura, que irá moviéndose entre los elementos del subgrupo; y borrado, que permitirá borrar todo el subgrupo menos el primer elemento tras haber acumulado la suma.

```
void juntalista(list<int> & l, int n){
	list<int>::const_iterator lectura, borrado;
	list<int>::iterator escritura;
	int contador;

	escritura = l.begin();
	while(escritura != l.end()){
		lectura = escritura;
		lectura++;
		borrado = lectura;

		contador = 1;
		while(contador<n && lectura != l.cend()){
			*escritura += *lectura;
			lectura++;
			contador++;
		}

		l.erase(borrado, lectura);
		escritura++;
	}
}
```
---

**Ejercicio 4. Implementar una función *void ordenag(list<int>* & *l, int m)* que dada una lista, ordene sus elementos a grupos de a m elementos. Por ejemplo si m=5, la función ordena los primeros 5 elementos entre sí, después los siguientes 5 y así sucesivamente. Si la longitud de la lista no es un múltiplo exacto de m elementos, entonces los últimos l.size()%m elementos se ordenan también. Ejemplo: lista={10, 1, 15, 7, 2, 12, 1, 9, 13, 3, 7, 6, 19, 15, 16, 11, 15}, con m=5 queda lista={1, 2, 7, 10, 15, 1, 3, 9, 12, 13, 6, 7, 15, 16, 19, 11, 15}.**

```
void ordenag(list<int> & l, int m){
	list<int>::iterator it = l.begin();
	while(it != l.end()){
		multiset<int> aux;
		list<int>::iterator posicion = it;
		int contador = 0;

		while(contador < m && it != l.end()){
			aux.insert((*it));
			it++;
			contador++;
		}

		posicion = l.erase(posicion, it);
		l.insert(posicion, aux.begin(), aux.end());		
	}
}
```
---

**Ejercicio 5. Implementar una función *int dminmax(list<int>* & *l)* que dada una lista l, devuelva la distancia entre las posiciones del mínimo y del máximo de la lista. La distancia debe ser positiva si el mínimo está antes del máximo y negativa en caso contrario. Ejemplo: lista1={5, 1, 3, 2, 4, 7, 6} devolvería 4, mientras que lista2={5, 9, 3, 2, 4, 1, 6} devolvería -4.**

**Si el valor del mínimo aparece repetido se debe tomar la primera posición en que aparece, mientras que para el máximo se debe tomar la última.**

```
int dminmax(list<int> & l){
	pair<int, int> min = {0, *(l.begin())};
	pair<int, int> max = min;
	list<int>::iterator it;

	int contador = 0;
	for(it = l.begin(); it != l.end(); it++){
		if((*it) < min.second)
			min = {contador,  *it};
		if((*it) >= max.second)
			max = {contador, *it};
		contador++;
	}

	return (max.first - min.first);
}
```
---

**Ejercicio 6. Implementar una función *bool lexiord(list<int>* & *l1, list<int>* & *l2)* que devuelve true si l1 es mayor (en sentido lexicográfico) que l2 y false en caso contrario.**

**El orden lexicográfico se define comparando los elementos en las posiciones equivalentes de ambas listas hasta encontrar uno diferente. La lista que tenga el valor más grande es la mayor en orden lexicográfico. Si una lista está totalmente contenida en otra, es mayor la más larga. Si son iguales la función devolverá false.**

**Ejemplo: lista1={1, 3, 2, 4, 6}, lista2={1, 3, 2, 5}, llamando a lexiord(lista1, lista2) obtendríamos false.**

```
bool lexiord(list<int> & l1, list<int> & l2){
	bool l1esmayor = true;

	if(l1.size()<l2.size())
		l1esmayor = false;

	list<int>::iterator it1 = l1.begin();
	list<int>::iterator it2 = l2.begin();
	while(l1esmayor && (it2 != l2.end())){
		if((*it1)<(*it2))
			l1esmayor = false;
		it1++;
		it2++;
	}

	return l1esmayor && (it1 != l1.end());
}
```
---

**Ejercicio 7. Implementar una función *void rotacion(queue<int>* & *C)* que saque una cierta cantidad de enteros del frente de la cola C y los vuelve a insertar al final de la cola de forma que queda en el frente el primer número par que haya en la cola.**

**Ejemplo: Dada C={1, 3, 5, 2, 4}, tras la rotación obtenemos C={2, 4, 1, 3, 5}.**

```
void rotacion(queue<int> & C){
	int contador = 0;
	bool par = false;
	while(!par && contador < C.size()){
		if(C.front()%2 != 0){
			C.push(C.front());
			C.pop();
			contador++;
		}
		else
			par = true;
	}
}
```
---
