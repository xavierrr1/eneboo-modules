/***************************************************************************
                 co_datosfiscales.qs  -  description
                             -------------------
    begin                : mie jun 1 2005
    copyright            : (C) 2005 by InfoSiAL S.L.
    email                : mail@infosial.com
 ***************************************************************************/
/***************************************************************************
 *                                                                         *
 *   This program is free software; you can redistribute it and/or modify  *
 *   it under the terms of the GNU General Public License as published by  *
 *   the Free Software Foundation; either version 2 of the License, or     *
 *   (at your option) any later version.                                   *
 *                                                                         *
 ***************************************************************************/

/** @file */

/** @class_declaration interna */
////////////////////////////////////////////////////////////////////////////
//// DECLARACION ///////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////

//////////////////////////////////////////////////////////////////
//// INTERNA /////////////////////////////////////////////////////
class interna {
	var ctx:Object;
	function interna( context ) { this.ctx = context; }
	function main() {
		this.ctx.interna_main();
	}
	function init() {
		this.ctx.interna_init();
	}
	function validateForm():Boolean {
		return this.ctx.interna_validateForm();
	}
}
//// INTERNA /////////////////////////////////////////////////////
//////////////////////////////////////////////////////////////////

/** @class_declaration oficial */
//////////////////////////////////////////////////////////////////
//// OFICIAL /////////////////////////////////////////////////////
class oficial extends interna {
	var bloqueoProvincia;
    function oficial( context ) { interna( context ); }
	function bufferChanged(fN) {
		return this.ctx.oficial_bufferChanged(fN);
	}
}
//// OFICIAL /////////////////////////////////////////////////////
//////////////////////////////////////////////////////////////////

/** @class_declaration head */
/////////////////////////////////////////////////////////////////
//// DESARROLLO /////////////////////////////////////////////////
class head extends oficial {
    function head( context ) { oficial ( context ); }
}
//// DESARROLLO /////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////

/** @class_declaration ifaceCtx*/
/////////////////////////////////////////////////////////////////
//// INTERFACE  /////////////////////////////////////////////////
class ifaceCtx extends head {
    function ifaceCtx( context ) { head( context ); }
}

const iface = new ifaceCtx( this );
//// INTERFACE  /////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////

/** @class_definition interna */
////////////////////////////////////////////////////////////////////////////
//// DEFINICION ////////////////////////////////////////////////////////////
////////////////////////////////////////////////////////////////////////////

//////////////////////////////////////////////////////////////////
//// INTERNA /////////////////////////////////////////////////////
/**
\C Los datos fiscales son únicos, por tanto el formulario no presenta los botones de navegación por registros.
\end

\D La gestión del formulario se hace de forma manual mediante el objeto f (FLFormSearchDB)
\end */
function interna_main()
{
		var f:Object = new FLFormSearchDB("co_datosfiscales");
		var cursor:FLSqlCursor = f.cursor();

		cursor.select();
		if (!cursor.first())
				cursor.setModeAccess(cursor.Insert);
		else
				cursor.setModeAccess(cursor.Edit);

		f.setMainWidget();
		if (cursor.modeAccess() == cursor.Insert)
				f.child("pushButtonCancel").setDisabled(true);
		cursor.refreshBuffer();
		var commitOk:Boolean = false;
		var acpt:Boolean;
		cursor.transaction(false);
		while (!commitOk) {
				acpt = false;
				f.exec("nombre");
				acpt = f.accepted();
				if (!acpt) {
						if (cursor.rollback())
								commitOk = true;
				} else {
						if (cursor.commitBuffer()) {
								cursor.commit();
								commitOk = true;
						}
				}
				f.close();
		}
}

function interna_validateForm():Boolean
{
	var cursor:FLSqlCursor = this.cursor();

	if (!flfactppal.iface.pub_validarProvincia(cursor)) {
		return false;
	}
}

function interna_init()
{
	var _i = this.iface;
	var cursor = this.cursor();

	_i.bloqueoProvincia = false;

	connect(cursor, "bufferChanged(QString)", _i, "bufferChanged");
	_i.bufferChanged("personafisica");
}

//// INTERNA /////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////

/** @class_definition oficial */
//////////////////////////////////////////////////////////////////
//// OFICIAL /////////////////////////////////////////////////////
function oficial_bufferChanged(fN:String)
{
	var util:FLUtil = new FLUtil();
	var cursor:FLSqlCursor = this.cursor();

	switch (fN) {
		case "provincia": {
			if (!this.iface.bloqueoProvincia) {
				this.iface.bloqueoProvincia = true;
				flfactppal.iface.pub_obtenerProvincia(this);
				this.iface.bloqueoProvincia = false;
			}
			break;
		}
		case "idprovincia": {
			if (cursor.valueBuffer("idprovincia") == 0) {
				cursor.setNull("idprovincia");
			}
			break;
		}
		case "personafisica": {
			if (cursor.valueBuffer("personafisica") == true) {
				this.child("fdbApellidosRS").setDisabled(true);
				this.child("fdbNombrePF").setDisabled(false);
				this.child("fdbApellidosPF").setDisabled(false);
			} else {
				this.child("fdbApellidosRS").setDisabled(false);
				this.child("fdbNombrePF").setValue("");
				this.child("fdbApellidosPF").setValue("");
				this.child("fdbNombrePF").setDisabled(true);
				this.child("fdbApellidosPF").setDisabled(true);
			}
			break;
		}
		case "nombrepf": 
		case "apellidospf": {
			valor = "";
			if (cursor.valueBuffer("apellidospf")) {
				valor += cursor.valueBuffer("apellidospf") + " ";
			}
			if (cursor.valueBuffer("nombrepf")) {
				valor += cursor.valueBuffer("nombrepf");
			}
			this.child("fdbApellidosRS").setValue(valor);
			break;
		}
	}
}
//// OFICIAL /////////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////

/** @class_definition head */
/////////////////////////////////////////////////////////////////
//// DESARROLLO /////////////////////////////////////////////////

//// DESARROLLO /////////////////////////////////////////////////
/////////////////////////////////////////////////////////////////
