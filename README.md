# study-postgres



```
Oracle      PostgreSQL	                            MySQL	                  Description
int[(n)]    bigint, int8	                          bigint	                signed eight-byte integer
　	        bigserial, serial8	　	                                        autoincrementing eight-byte integer
　	        bit[]	　	                                                      fixed-length bit string
　	        bit varying[],                          varbit	　	            variable-length bit string
　	        boolean, bool	                          boolean, bool	          logical Boolean (true/false)
　	        box	　	                                                        rectangular box in the plane
　	        bytea	　	                                                      binary data ("byte array")
varchar2    character varying[], varchar[]          varchar                 variable-length character string
char        character[], char[]	                    char                    fixed-length character string
　	cidr	　	IPv4 or IPv6 network address
　	circle	　	circle in the plane
　	date	　	calendar date (year, month, day)
　	double precision,float8	　	double precision floating-point number
　	　	enum	enumeration type, max 65,535 elements
　	inet	　	IPv4 or IPv6 host address
int[(n)]	integer,int,in4	int	signed four-byte integer
　	interval [ (p) ]	　	time span
　	line	　	infinite line in the plane
　	lseg	　	line segment in the plane
　	macaddr	　	MAC address
　	money	　	currency amount
number	numeric [ (p, s) ],decimal[(p,s)]	int	exact numeric of selectable precision
　	path	　	geometric path in the plane
　	point	　	geometric point in the plane
　	polygon	　	closed geometric path in the plane
　	real,float4	　	single precision floating-point number
int[(n)]	smallint,int2	smallint	signed two-byte integer
　	serial,serial4	　	autoincrementing four-byte integer
blob, cblob	text	blob, text	variable-length character string
　	time [ (p) ] [ without time zone ]	time	time of day
　	time [ (p) ] with time zone, timetz	　	time of day, including time zone
　	timestamp [ (p) ] [ without time zone ]	timestamp	date and time
　	timestamp [ (p) ] with time zone, timestamptz	　	date and time, inc
```
