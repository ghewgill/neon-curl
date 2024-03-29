import os
import shutil
import stat
import sys
import tarfile

env = Environment()
if sys.platform == "win32":
    env.Append(CXXFLAGS=["/EHsc", "/MDd", "/W4", "/WX"])
else:
    env.Append(CXXFLAGS=["-std=c++0x"])

libz = None

libzenv = Environment()
if "RELEASE" not in env or not env["RELEASE"]:
    if sys.platform == "win32":
        libzenv.Append(CFLAGS=[
            "/MDd",
            "/Zi",
            "/Od",
        ])

if GetOption("clean"):
    shutil.rmtree("zlib-1.2.8", ignore_errors=True)
elif not os.path.exists("zlib-1.2.8/configure"):
    tarfile.open("zlib-1.2.8.tar.gz").extractall(".")

if sys.platform == "win32":
    libz = libzenv.Library("zlib-1.2.8/zlib.lib", [
        "zlib-1.2.8/adler32.c",
        "zlib-1.2.8/compress.c",
        "zlib-1.2.8/crc32.c",
        "zlib-1.2.8/deflate.c",
        "zlib-1.2.8/inffast.c",
        "zlib-1.2.8/inflate.c",
        "zlib-1.2.8/inftrees.c",
        "zlib-1.2.8/trees.c",
        "zlib-1.2.8/uncompr.c",
        "zlib-1.2.8/zutil.c",
    ])
    env.Append(CPPPATH=["zlib-1.2.8"])
else:
    conf = Configure(env)
    if not conf.CheckLibWithHeader("z", "zlib.h", "C++"):
        libz = libzenv.Command("zlib-1.2.8/libz.a", "zlib-1.2.8/configure", "cd zlib-1.2.8 && ./configure --static && make")
        env.Append(CPPPATH=["zlib-1.2.8"])
    conf.Finish()

def replace(fn, t, r):
    data = open(fn).read()
    data = data.replace(t, r)
    open(fn, "w").write(data)

libssl = None

sslenv = Environment()
if "RELEASE" not in env or not env["RELEASE"]:
    if sys.platform == "win32":
        sslenv.Append(CFLAGS=[
            "/MDd",
            "/Zi",
            "/Od",
        ])

if GetOption("clean"):
    def remove_readonly(function, path, excinfo):
        if os.path.exists(path):
            os.chmod(path, stat.S_IWRITE)
            os.remove(path)
    shutil.rmtree("libressl-2.2.4", onerror=remove_readonly)
elif not os.path.exists("libressl-2.2.4/configure"):
    tarfile.open("libressl-2.2.4.tar.gz").extractall(".")
    if sys.platform == "win32":
        replace("libressl-2.2.4/include/compat/string.h", "static inline", "static")
        replace("libressl-2.2.4/crypto/md32_common.h", "static inline", "static")
        replace("libressl-2.2.4/crypto/compat/posix_win.c", "#include <windows.h>", "")

if sys.platform == "win32":
    # This is just enough of libressl to build what we currently need.
    sslenv.Append(CPPDEFINES=["inline="])
    sslenv.Append(CPPPATH=["libressl-2.2.4/include"])
    sslenv.Append(CPPPATH=["libressl-2.2.4/include/compat"])
    sslenv.Append(CPPPATH=["libressl-2.2.4/crypto"])
    sslenv.Append(CPPPATH=["libressl-2.2.4/crypto/asn1"])
    sslenv.Append(CPPPATH=["libressl-2.2.4/crypto/evp"])
    sslenv.Append(CPPPATH=["libressl-2.2.4/crypto/modes"])
    libssl = sslenv.Library("libressl-2.2.4/libssl.lib", [
        "libressl-2.2.4/crypto/aes/aes_cbc.c",
        "libressl-2.2.4/crypto/aes/aes_core.c",
        "libressl-2.2.4/crypto/asn1/a_bitstr.c",
        "libressl-2.2.4/crypto/asn1/a_bool.c",
        "libressl-2.2.4/crypto/asn1/a_bytes.c",
        "libressl-2.2.4/crypto/asn1/a_d2i_fp.c",
        "libressl-2.2.4/crypto/asn1/a_digest.c",
        "libressl-2.2.4/crypto/asn1/a_dup.c",
        "libressl-2.2.4/crypto/asn1/a_enum.c",
        "libressl-2.2.4/crypto/asn1/a_gentm.c",
        "libressl-2.2.4/crypto/asn1/a_i2d_fp.c",
        "libressl-2.2.4/crypto/asn1/a_int.c",
        "libressl-2.2.4/crypto/asn1/a_mbstr.c",
        "libressl-2.2.4/crypto/asn1/a_object.c",
        "libressl-2.2.4/crypto/asn1/a_octet.c",
        "libressl-2.2.4/crypto/asn1/a_print.c",
        "libressl-2.2.4/crypto/asn1/a_set.c",
        "libressl-2.2.4/crypto/asn1/a_sign.c",
        "libressl-2.2.4/crypto/asn1/a_strex.c",
        "libressl-2.2.4/crypto/asn1/a_strnid.c",
        "libressl-2.2.4/crypto/asn1/a_time.c",
        "libressl-2.2.4/crypto/asn1/a_type.c",
        "libressl-2.2.4/crypto/asn1/a_utctm.c",
        "libressl-2.2.4/crypto/asn1/a_utf8.c",
        "libressl-2.2.4/crypto/asn1/a_verify.c",
        "libressl-2.2.4/crypto/asn1/ameth_lib.c",
        "libressl-2.2.4/crypto/asn1/asn_pack.c",
        "libressl-2.2.4/crypto/asn1/asn1_gen.c",
        "libressl-2.2.4/crypto/asn1/asn1_lib.c",
        "libressl-2.2.4/crypto/asn1/asn1_par.c",
        "libressl-2.2.4/crypto/asn1/d2i_pr.c",
        "libressl-2.2.4/crypto/asn1/evp_asn1.c",
        "libressl-2.2.4/crypto/asn1/f_int.c",
        "libressl-2.2.4/crypto/asn1/f_string.c",
        "libressl-2.2.4/crypto/asn1/i2d_pr.c",
        "libressl-2.2.4/crypto/asn1/p5_pbe.c",
        "libressl-2.2.4/crypto/asn1/p5_pbev2.c",
        "libressl-2.2.4/crypto/asn1/p8_pkey.c",
        "libressl-2.2.4/crypto/asn1/t_pkey.c",
        "libressl-2.2.4/crypto/asn1/t_x509.c",
        "libressl-2.2.4/crypto/asn1/t_x509a.c",
        "libressl-2.2.4/crypto/asn1/tasn_dec.c",
        "libressl-2.2.4/crypto/asn1/tasn_enc.c",
        "libressl-2.2.4/crypto/asn1/tasn_fre.c",
        "libressl-2.2.4/crypto/asn1/tasn_new.c",
        "libressl-2.2.4/crypto/asn1/tasn_prn.c",
        "libressl-2.2.4/crypto/asn1/tasn_typ.c",
        "libressl-2.2.4/crypto/asn1/tasn_utl.c",
        "libressl-2.2.4/crypto/asn1/x_algor.c",
        "libressl-2.2.4/crypto/asn1/x_attrib.c",
        "libressl-2.2.4/crypto/asn1/x_bignum.c",
        "libressl-2.2.4/crypto/asn1/x_crl.c",
        "libressl-2.2.4/crypto/asn1/x_exten.c",
        "libressl-2.2.4/crypto/asn1/x_long.c",
        "libressl-2.2.4/crypto/asn1/x_name.c",
        "libressl-2.2.4/crypto/asn1/x_pubkey.c",
        "libressl-2.2.4/crypto/asn1/x_req.c",
        "libressl-2.2.4/crypto/asn1/x_sig.c",
        "libressl-2.2.4/crypto/asn1/x_spki.c",
        "libressl-2.2.4/crypto/asn1/x_val.c",
        "libressl-2.2.4/crypto/asn1/x_x509.c",
        "libressl-2.2.4/crypto/asn1/x_x509a.c",
        "libressl-2.2.4/crypto/bio/b_dump.c",
        "libressl-2.2.4/crypto/bio/b_print.c",
        "libressl-2.2.4/crypto/bio/bio_lib.c",
        "libressl-2.2.4/crypto/bio/bss_file.c",
        "libressl-2.2.4/crypto/bio/bss_mem.c",
        "libressl-2.2.4/crypto/bio/bss_null.c",
        "libressl-2.2.4/crypto/bn/bn_add.c",
        "libressl-2.2.4/crypto/bn/bn_asm.c",
        "libressl-2.2.4/crypto/bn/bn_blind.c",
        "libressl-2.2.4/crypto/bn/bn_ctx.c",
        "libressl-2.2.4/crypto/bn/bn_div.c",
        "libressl-2.2.4/crypto/bn/bn_exp.c",
        "libressl-2.2.4/crypto/bn/bn_exp2.c",
        "libressl-2.2.4/crypto/bn/bn_gcd.c",
        "libressl-2.2.4/crypto/bn/bn_gf2m.c",
        "libressl-2.2.4/crypto/bn/bn_kron.c",
        "libressl-2.2.4/crypto/bn/bn_lib.c",
        "libressl-2.2.4/crypto/bn/bn_mod.c",
        "libressl-2.2.4/crypto/bn/bn_mont.c",
        "libressl-2.2.4/crypto/bn/bn_mul.c",
        "libressl-2.2.4/crypto/bn/bn_nist.c",
        "libressl-2.2.4/crypto/bn/bn_prime.c",
        "libressl-2.2.4/crypto/bn/bn_print.c",
        "libressl-2.2.4/crypto/bn/bn_rand.c",
        "libressl-2.2.4/crypto/bn/bn_recp.c",
        "libressl-2.2.4/crypto/bn/bn_shift.c",
        "libressl-2.2.4/crypto/bn/bn_sqr.c",
        "libressl-2.2.4/crypto/bn/bn_sqrt.c",
        "libressl-2.2.4/crypto/bn/bn_word.c",
        "libressl-2.2.4/crypto/buffer/buffer.c",
        "libressl-2.2.4/crypto/cmac/cm_ameth.c",
        "libressl-2.2.4/crypto/cmac/cm_pmeth.c",
        "libressl-2.2.4/crypto/cmac/cmac.c",
        "libressl-2.2.4/crypto/compat/arc4random.c",
        "libressl-2.2.4/crypto/compat/bsd-asprintf.c",
        "libressl-2.2.4/crypto/compat/explicit_bzero_win.c",
        "libressl-2.2.4/crypto/compat/getentropy_win.c",
        "libressl-2.2.4/crypto/compat/posix_win.c",
        "libressl-2.2.4/crypto/compat/reallocarray.c",
        "libressl-2.2.4/crypto/compat/strcasecmp.c",
        "libressl-2.2.4/crypto/compat/strlcat.c",
        "libressl-2.2.4/crypto/compat/strlcpy.c",
        "libressl-2.2.4/crypto/compat/timingsafe_memcmp.c",
        "libressl-2.2.4/crypto/conf/conf_api.c",
        "libressl-2.2.4/crypto/conf/conf_def.c",
        "libressl-2.2.4/crypto/conf/conf_lib.c",
        "libressl-2.2.4/crypto/conf/conf_mod.c",
        "libressl-2.2.4/crypto/cryptlib.c",
        "libressl-2.2.4/crypto/dh/dh_ameth.c",
        "libressl-2.2.4/crypto/dh/dh_asn1.c",
        "libressl-2.2.4/crypto/dh/dh_check.c",
        "libressl-2.2.4/crypto/dh/dh_gen.c",
        "libressl-2.2.4/crypto/dh/dh_key.c",
        "libressl-2.2.4/crypto/dh/dh_lib.c",
        "libressl-2.2.4/crypto/dh/dh_pmeth.c",
        "libressl-2.2.4/crypto/dsa/dsa_ameth.c",
        "libressl-2.2.4/crypto/dsa/dsa_asn1.c",
        "libressl-2.2.4/crypto/dsa/dsa_gen.c",
        "libressl-2.2.4/crypto/dsa/dsa_key.c",
        "libressl-2.2.4/crypto/dsa/dsa_lib.c",
        "libressl-2.2.4/crypto/dsa/dsa_ossl.c",
        "libressl-2.2.4/crypto/dsa/dsa_pmeth.c",
        "libressl-2.2.4/crypto/dsa/dsa_sign.c",
        "libressl-2.2.4/crypto/dsa/dsa_vrf.c",
        "libressl-2.2.4/crypto/dso/dso_lib.c",
        "libressl-2.2.4/crypto/dso/dso_null.c",
        "libressl-2.2.4/crypto/dso/dso_openssl.c",
        "libressl-2.2.4/crypto/ec/ec_ameth.c",
        "libressl-2.2.4/crypto/ec/ec_asn1.c",
        "libressl-2.2.4/crypto/ec/ec_curve.c",
        "libressl-2.2.4/crypto/ec/ec_cvt.c",
        "libressl-2.2.4/crypto/ec/ec_key.c",
        "libressl-2.2.4/crypto/ec/ec_lib.c",
        "libressl-2.2.4/crypto/ec/ec_mult.c",
        "libressl-2.2.4/crypto/ec/ec_oct.c",
        "libressl-2.2.4/crypto/ec/ec_pmeth.c",
        "libressl-2.2.4/crypto/ec/ec_print.c",
        "libressl-2.2.4/crypto/ec/ec2_mult.c",
        "libressl-2.2.4/crypto/ec/ec2_oct.c",
        "libressl-2.2.4/crypto/ec/ec2_smpl.c",
        "libressl-2.2.4/crypto/ec/eck_prn.c",
        "libressl-2.2.4/crypto/ec/ecp_mont.c",
        "libressl-2.2.4/crypto/ec/ecp_nist.c",
        "libressl-2.2.4/crypto/ec/ecp_oct.c",
        "libressl-2.2.4/crypto/ec/ecp_smpl.c",
        "libressl-2.2.4/crypto/ecdh/ech_key.c",
        "libressl-2.2.4/crypto/ecdh/ech_lib.c",
        "libressl-2.2.4/crypto/ecdh/ech_ossl.c",
        "libressl-2.2.4/crypto/ecdsa/ecs_asn1.c",
        "libressl-2.2.4/crypto/ecdsa/ecs_lib.c",
        "libressl-2.2.4/crypto/ecdsa/ecs_ossl.c",
        "libressl-2.2.4/crypto/ecdsa/ecs_sign.c",
        "libressl-2.2.4/crypto/ecdsa/ecs_vrf.c",
        "libressl-2.2.4/crypto/engine/eng_init.c",
        "libressl-2.2.4/crypto/engine/eng_lib.c",
        "libressl-2.2.4/crypto/engine/eng_list.c",
        "libressl-2.2.4/crypto/engine/eng_table.c",
        "libressl-2.2.4/crypto/engine/tb_asnmth.c",
        "libressl-2.2.4/crypto/engine/tb_cipher.c",
        "libressl-2.2.4/crypto/engine/tb_dh.c",
        "libressl-2.2.4/crypto/engine/tb_digest.c",
        "libressl-2.2.4/crypto/engine/tb_dsa.c",
        "libressl-2.2.4/crypto/engine/tb_ecdh.c",
        "libressl-2.2.4/crypto/engine/tb_ecdsa.c",
        "libressl-2.2.4/crypto/engine/tb_pkmeth.c",
        "libressl-2.2.4/crypto/engine/tb_rsa.c",
        "libressl-2.2.4/crypto/err/err.c",
        "libressl-2.2.4/crypto/err/err_prn.c",
        "libressl-2.2.4/crypto/evp/bio_enc.c",
        "libressl-2.2.4/crypto/evp/bio_md.c",
        "libressl-2.2.4/crypto/evp/digest.c",
        "libressl-2.2.4/crypto/evp/e_aes.c",
        "libressl-2.2.4/crypto/evp/evp_enc.c",
        "libressl-2.2.4/crypto/evp/evp_lib.c",
        "libressl-2.2.4/crypto/evp/evp_pbe.c",
        "libressl-2.2.4/crypto/evp/evp_pkey.c",
        "libressl-2.2.4/crypto/evp/m_md5.c",
        "libressl-2.2.4/crypto/evp/m_sha1.c",
        "libressl-2.2.4/crypto/evp/m_sigver.c",
        "libressl-2.2.4/crypto/evp/names.c",
        "libressl-2.2.4/crypto/evp/p_lib.c",
        "libressl-2.2.4/crypto/evp/p_sign.c",
        "libressl-2.2.4/crypto/evp/p_verify.c",
        "libressl-2.2.4/crypto/evp/p5_crpt.c",
        "libressl-2.2.4/crypto/evp/p5_crpt2.c",
        "libressl-2.2.4/crypto/evp/pmeth_fn.c",
        "libressl-2.2.4/crypto/evp/pmeth_gn.c",
        "libressl-2.2.4/crypto/evp/pmeth_lib.c",
        "libressl-2.2.4/crypto/ex_data.c",
        "libressl-2.2.4/crypto/gost/gost_asn1.c",
        "libressl-2.2.4/crypto/gost/gost2814789.c",
        "libressl-2.2.4/crypto/gost/gost89_keywrap.c",
        "libressl-2.2.4/crypto/gost/gost89_params.c",
        "libressl-2.2.4/crypto/gost/gost89imit_ameth.c",
        "libressl-2.2.4/crypto/gost/gost89imit_pmeth.c",
        "libressl-2.2.4/crypto/gost/gostr341001.c",
        "libressl-2.2.4/crypto/gost/gostr341001_ameth.c",
        "libressl-2.2.4/crypto/gost/gostr341001_key.c",
        "libressl-2.2.4/crypto/gost/gostr341001_params.c",
        "libressl-2.2.4/crypto/gost/gostr341001_pmeth.c",
        "libressl-2.2.4/crypto/gost/gostr341194.c",
        "libressl-2.2.4/crypto/gost/streebog.c",
        "libressl-2.2.4/crypto/hmac/hm_ameth.c",
        "libressl-2.2.4/crypto/hmac/hm_pmeth.c",
        "libressl-2.2.4/crypto/hmac/hmac.c",
        "libressl-2.2.4/crypto/lhash/lhash.c",
        "libressl-2.2.4/crypto/malloc-wrapper.c",
        "libressl-2.2.4/crypto/md5/md5_dgst.c",
        "libressl-2.2.4/crypto/mem_clr.c",
        "libressl-2.2.4/crypto/mem_dbg.c",
        "libressl-2.2.4/crypto/modes/cbc128.c",
        "libressl-2.2.4/crypto/modes/ccm128.c",
        "libressl-2.2.4/crypto/modes/cfb128.c",
        "libressl-2.2.4/crypto/modes/ctr128.c",
        "libressl-2.2.4/crypto/modes/gcm128.c",
        "libressl-2.2.4/crypto/modes/ofb128.c",
        "libressl-2.2.4/crypto/modes/xts128.c",
        "libressl-2.2.4/crypto/o_init.c",
        "libressl-2.2.4/crypto/o_time.c",
        "libressl-2.2.4/crypto/objects/o_names.c",
        "libressl-2.2.4/crypto/objects/obj_dat.c",
        "libressl-2.2.4/crypto/objects/obj_lib.c",
        "libressl-2.2.4/crypto/objects/obj_xref.c",
        "libressl-2.2.4/crypto/ocsp/ocsp_asn.c",
        "libressl-2.2.4/crypto/pkcs12/p12_add.c",
        "libressl-2.2.4/crypto/pkcs12/p12_asn.c",
        "libressl-2.2.4/crypto/pkcs12/p12_crpt.c",
        "libressl-2.2.4/crypto/pkcs12/p12_decr.c",
        "libressl-2.2.4/crypto/pkcs12/p12_key.c",
        "libressl-2.2.4/crypto/pkcs12/p12_p8d.c",
        "libressl-2.2.4/crypto/pkcs12/p12_p8e.c",
        "libressl-2.2.4/crypto/pkcs12/p12_utl.c",
        "libressl-2.2.4/crypto/pkcs7/pk7_asn1.c",
        "libressl-2.2.4/crypto/pkcs7/pk7_attr.c",
        "libressl-2.2.4/crypto/pkcs7/pk7_doit.c",
        "libressl-2.2.4/crypto/pkcs7/pk7_lib.c",
        "libressl-2.2.4/crypto/rand/rand_lib.c",
        "libressl-2.2.4/crypto/rsa/rsa_ameth.c",
        "libressl-2.2.4/crypto/rsa/rsa_asn1.c",
        "libressl-2.2.4/crypto/rsa/rsa_crpt.c",
        "libressl-2.2.4/crypto/rsa/rsa_eay.c",
        "libressl-2.2.4/crypto/rsa/rsa_gen.c",
        "libressl-2.2.4/crypto/rsa/rsa_lib.c",
        "libressl-2.2.4/crypto/rsa/rsa_none.c",
        "libressl-2.2.4/crypto/rsa/rsa_oaep.c",
        "libressl-2.2.4/crypto/rsa/rsa_pk1.c",
        "libressl-2.2.4/crypto/rsa/rsa_pmeth.c",
        "libressl-2.2.4/crypto/rsa/rsa_pss.c",
        "libressl-2.2.4/crypto/rsa/rsa_sign.c",
        "libressl-2.2.4/crypto/rsa/rsa_ssl.c",
        "libressl-2.2.4/crypto/rsa/rsa_x931.c",
        "libressl-2.2.4/crypto/sha/sha1_one.c",
        "libressl-2.2.4/crypto/sha/sha1dgst.c",
        "libressl-2.2.4/crypto/sha/sha256.c",
        "libressl-2.2.4/crypto/sha/sha512.c",
        "libressl-2.2.4/crypto/stack/stack.c",
        "libressl-2.2.4/crypto/x509/x_all.c",
        "libressl-2.2.4/crypto/x509/x509_att.c",
        "libressl-2.2.4/crypto/x509/x509_cmp.c",
        "libressl-2.2.4/crypto/x509/x509_def.c",
        "libressl-2.2.4/crypto/x509/x509_ext.c",
        "libressl-2.2.4/crypto/x509/x509_lu.c",
        "libressl-2.2.4/crypto/x509/x509_obj.c",
        "libressl-2.2.4/crypto/x509/x509_req.c",
        "libressl-2.2.4/crypto/x509/x509_trs.c",
        "libressl-2.2.4/crypto/x509/x509_v3.c",
        "libressl-2.2.4/crypto/x509/x509_vfy.c",
        "libressl-2.2.4/crypto/x509/x509_vpm.c",
        "libressl-2.2.4/crypto/x509/x509name.c",
        "libressl-2.2.4/crypto/x509/x509rset.c",
        "libressl-2.2.4/crypto/x509v3/pcy_cache.c",
        "libressl-2.2.4/crypto/x509v3/pcy_data.c",
        "libressl-2.2.4/crypto/x509v3/pcy_lib.c",
        "libressl-2.2.4/crypto/x509v3/pcy_map.c",
        "libressl-2.2.4/crypto/x509v3/pcy_node.c",
        "libressl-2.2.4/crypto/x509v3/pcy_tree.c",
        "libressl-2.2.4/crypto/x509v3/v3_akey.c",
        "libressl-2.2.4/crypto/x509v3/v3_akeya.c",
        "libressl-2.2.4/crypto/x509v3/v3_alt.c",
        "libressl-2.2.4/crypto/x509v3/v3_bcons.c",
        "libressl-2.2.4/crypto/x509v3/v3_bitst.c",
        "libressl-2.2.4/crypto/x509v3/v3_conf.c",
        "libressl-2.2.4/crypto/x509v3/v3_cpols.c",
        "libressl-2.2.4/crypto/x509v3/v3_crld.c",
        "libressl-2.2.4/crypto/x509v3/v3_enum.c",
        "libressl-2.2.4/crypto/x509v3/v3_extku.c",
        "libressl-2.2.4/crypto/x509v3/v3_genn.c",
        "libressl-2.2.4/crypto/x509v3/v3_ia5.c",
        "libressl-2.2.4/crypto/x509v3/v3_info.c",
        "libressl-2.2.4/crypto/x509v3/v3_int.c",
        "libressl-2.2.4/crypto/x509v3/v3_lib.c",
        "libressl-2.2.4/crypto/x509v3/v3_ncons.c",
        "libressl-2.2.4/crypto/x509v3/v3_ocsp.c",
        "libressl-2.2.4/crypto/x509v3/v3_pci.c",
        "libressl-2.2.4/crypto/x509v3/v3_pcia.c",
        "libressl-2.2.4/crypto/x509v3/v3_pcons.c",
        "libressl-2.2.4/crypto/x509v3/v3_pku.c",
        "libressl-2.2.4/crypto/x509v3/v3_pmaps.c",
        "libressl-2.2.4/crypto/x509v3/v3_prn.c",
        "libressl-2.2.4/crypto/x509v3/v3_purp.c",
        "libressl-2.2.4/crypto/x509v3/v3_skey.c",
        "libressl-2.2.4/crypto/x509v3/v3_sxnet.c",
        "libressl-2.2.4/crypto/x509v3/v3_utl.c",
    ])
    env.Append(CPPPATH=["libressl-2.2.4/include"])
else:
    conf = Configure(env)
    if not conf.CheckLibWithHeader("ressl", "crypto.h", "C++"):
        libssl = sslenv.Command(["lib/libssl.a", "lib/libcrypto.a"], "libressl-2.2.4/configure", "cd libressl-2.2.4 && ./configure --with-pic --prefix={} && make install".format(os.path.abspath(".")))
        env.Append(CPPPATH=["include"])
    conf.Finish()

libcurl = None

curlenv = Environment()

if GetOption("clean"):
    shutil.rmtree("curl-7.41.0", ignore_errors=True)
elif not os.path.exists("curl-7.41.0/configure"):
    tarfile.open("curl-7.41.0.tar.gz").extractall(".")
    if sys.platform == "win32":
        config_win32 = open("curl-7.41.0/lib/config-win32.h").read()
        config_win32 = "#define HTTP_ONLY\n" + config_win32
        config_win32 = "#define USE_WINDOWS_SSPI\n" + config_win32
        config_win32 = "#define USE_SCHANNEL\n" + config_win32
        open("curl-7.41.0/lib/config-win32.h", "w").write(config_win32)

if sys.platform == "win32":
    env.Append(CPPFLAGS=["-DCURL_STATICLIB"])
    curlenv.Append(CPPFLAGS=["-DCURL_STATICLIB"])
    libname = "libcurl" if "RELEASE" in env and env["RELEASE"] else "libcurld"
    cfgname = "release" if "RELEASE" in env and env["RELEASE"] else "debug"
    cfgname += "-zlib"
    libcurl = curlenv.Command("curl-7.41.0/lib/{}.lib".format(libname), "curl-7.41.0/winbuild/Makefile.vc", "cd curl-7.41.0/lib && nmake /f Makefile.vc10 CFG={} MACHINE=x64".format(cfgname))
    env.Append(CPPPATH=["curl-7.41.0/include"])
    env.Append(LIBS=["advapi32", "ws2_32"])
elif sys.platform == "darwin":
    # MacOS provides libcurl.
    libcurl = "curl"
else:
    conf = Configure(env)
    if not conf.CheckLibWithHeader("curl", "curl/curl.h", "C++"):
        libcurl = curlenv.Command("curl-7.41.0/lib/.libs/libcurl.a", "curl-7.41.0/configure", " ".join(["cd curl-7.41.0 &&",
            "./configure",
            "--disable-ares",
            "--disable-ftp",
            "--disable-file",
            "--disable-ldap",
            "--disable-ldaps",
            "--disable-rtsp",
            "--disable-proxy",
            "--disable-dict",
            "--disable-telnet",
            "--disable-tftp",
            "--disable-pop3",
            "--disable-imap",
            "--disable-smb",
            "--disable-smtp",
            "--disable-gopher",
            "--disable-manual",
            "--disable-ipv6",
            "--disable-threaded-resolver",
            "--disable-sspi",
            "--disable-crypto-auth",
            "--disable-ntlm-wb",
            "--disable-tls-srp",
            "--disable-unix-sockets",
            "--disable-cookies",
            "--disable-soname-bump",
            "--without-winssl",
            "--without-darwinssl",
            "--without-gnutls",
            "--without-polarssl",
            "--without-cyassl",
            "--without-nss",
            "--without-axtls",
            "--without-libmetalink",
            "--without-libssh2",
            "--without-librtmp",
            "--without-winidn",
            "--without-libidn",
            "--without-nghttp2",
            "--with-ssl=" + os.path.abspath("."),
            "&& make"]))
        env.Append(CPPPATH=["curl-7.41.0/include"])
    conf.Finish()

env.Depends(libcurl, libz)
env.Depends(libcurl, libssl)

env.Append(CPPPATH=["../../common"])
env.Append(LIBS=[x for x in [libcurl, libssl, libz] if x])
env.SharedLibrary("neon_curl", "curl.cpp")
