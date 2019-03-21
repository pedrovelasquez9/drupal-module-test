# Módulos con Drupal

### Introducción

Este repositorio almacena dos módulos sencillos para Drupal:

+ test_pedro
+ nombre_usuario

### Información del entorno

+ Drupal 7.65
+ PHP 7.0.33

### Funcionamiento

La dinámica del funcionamiento de los módulos es la siguiente:

+ Se tiene un enlace, mostrado una vez se inicie sesión en Drupal, que lleva a la vista del módulo **test_pedro**, en este módulo se tiene un botón que redirecciona al módulo nombre_usuario y muestra un mensaje de bienvenida al usuario que está actualmente logueado en Drupal.

### Instalación

Para instalar/desarrollar un módulo en Drupal debe crearse una carpeta por módulo en el directorio **../drupal/sites/all/modules/**, la carpeta debe tener el nombre del módulo que se quiere agregar.

Dentro del directorio del módulo, debemos tener dos archivos:

+ **nombre_modulo.info:** almacena información del módulo desarrollado, por ejemplo:
	
	name = Test de Pedro
	description = Módulo de prueba con Drupal
	core = 7.x
	package = My Modules

+ **nombre_modulo.module:** almacena la definición de nuestro módulo, aquí es donde escribiremos el código php con la lógica de nuestro módulo Drupal. Por ejemplo:

	<?php

	function test_pedro_menu() {
	$items = array();

	$items['test_pedro/test_pedro'] = array( 
		'title' => 'Test de Pedro', 
		'description' => 'Un módulo de prueba',
		'page callback' => 'drupal_get_form', 
		'page arguments' => array('test_pedro_form'), 
		'access callback' => 'user_is_logged_in',
		'type' => MENU_NORMAL_ITEM
	);

	return $items;
	}

	function test_pedro_form($form, &$form_state) {
	
	$form['submit_button'] = array(
		'#type' => 'submit',
		'#value' => t('Ir a bienvenida'),
	);

	$form['#submit'][] = 'test_pedro_form_submit';
	
	return $form;
	}


	function test_pedro_form_submit($form, &$form_state) {
		drupal_goto('nombre_usuario/nombre_usuario');
	}

**NOTA:** para poder visualizar y usar los módulos, debe irse a la sección **modules > my modules** de Drupal y activar los módulos. Si no se visualizan los cambios al modificar el código de un módulo, debe desactivarse y activarse desde este apartado.

### Menú

Para que nuestro módulo aparezca en el menú de Drupal, debemos tener una función **nombre_modulo_menu** con el siguiente contenido:

	$items['test_pedro/test_pedro'] = array( //crea la url de acceso al módulo "test_pedro/test_pedro"
		'title' => 'Test de Pedro', //título de la página del módulo
		'description' => 'Un módulo de prueba', //descripción del módulo
		'page callback' => 'drupal_get_form', //hace referencia a la función que se ejecutará al entrar al módulo
		'page arguments' => array('test_pedro_form'), //Argumentos a recibir al entrar al módulo, en este caso, recibe el form a renderizar
		'access callback' => 'user_is_logged_in', //define si todos los usuarios podrán acceder al módulo o solo los usuarios que hayan iniciado sesión
		'type' => MENU_NORMAL_ITEM //Tipo de item de menú definido 
	);

	return $items;
	}

### Redirección

El módulo **test_pedro** contiene un formulario que consta de un botón submit, para definir la acción que realizará el botón, debe definirse una función **nombre_modulo_form_submit**, a continuación, vemos la definición del form y de la función a ejecutar en el submit:

	function test_pedro_form($form, &$form_state) {
  
	$form['submit_button'] = array( //definición del elemento del form
		'#type' => 'submit',
		'#value' => t('Ir a bienvenida'),
	);

	$form['#submit'][] = 'test_pedro_form_submit'; //definición del método que llamará el elemento
	
	return $form;
	}


	function test_pedro_form_submit($form, &$form_state) { //método del submit
		drupal_goto('nombre_usuario/nombre_usuario'); //redirección a otro módulo
	}

### Acceso a variables de sesión

En cuanto al módulo **nombre_usuario**, para acceder a los datos de la sesión activa del usuario logueado, basta con acceder al objeto global **$user**:

	function nombre_usuario_void() {
		global $user; //se accede al objeto global
		return $user->name; //se retorna la propiedad que queremos mostrar en la vista del módulo
	}