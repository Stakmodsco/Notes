mike@Mikes-MacBook-Pro Safeguard-app-my-fix-main % cd /Users/mike/Desktop/Safeguard-app-my-fix-main && printf '\n=== PROTECTED BACKEND ENDPOINTS ===\n' && sed -n '1000,1030p' backend/server.py && sed -n '1075,1100p' backend/server.py && sed -n '1135,1160p' backend/server.py && printf '\n=== SERVICE TRIAL RECORD LOOKUP ===\n' && sed -n '25,85p' backend/subscription_access.py


=== PROTECTED BACKEND ENDPOINTS ===
        "user_id": user["id"],
    })
    return status_obj

@api_router.get("/status", response_model=List[StatusCheck])
async def get_status_checks(user: dict = Depends(require_auth)):
    # Motor's mapping filter is parameterized; untrusted text is never
    # interpolated into executable query syntax.
    status_checks = await db.status_checks.find(
        {"user_id": user["id"]}
    ).to_list(1000)
    return [StatusCheck(**status_check) for status_check in status_checks]

@api_router.post("/extract-document-upload", response_model=DocumentExtractionResponse)
async def extract_document_upload(
    request: Request,
    file: UploadFile = File(...),
    user: dict = Depends(require_auth),
):
    """Stream and analyse an image or PDF without base64 JSON overhead."""
    await require_plus_access(db, user)
    await check_rate_limit(request, "extract_document", user)

    temp_path = None
    s3_key = None

    try:
        mime_type, temp_path, total_bytes = await _spool_upload_to_temp(file)
        logger.info(
            "[UPLOAD] Document upload spooled user=%s mime=%s size=%.1f KB",
            user.get("id", "unknown"),
            except OSError:
                pass
        if s3_key:
            await asyncio.to_thread(_delete_temp_document_from_s3, s3_key)


@api_router.post("/extract-document", response_model=DocumentExtractionResponse)
async def extract_document(
    request_body: DocumentExtractionRequest,
    request: Request,
    user: dict = Depends(require_auth)
):
    """Extract document information from an image using AI.
    
    SECURITY: Requires authentication and rate limiting.
    """
    # Rate limiting
    await require_plus_access(db, user)
    await check_rate_limit(request, "extract_document", user)
    
    logger.info(f"[EXTRACT DOC] User {user.get('email', 'unknown')} requesting document extraction")
    
    # Validate base64
    try:
        # Check if it's valid base64
        if not request_body.image_base64:
        )

@api_router.post("/convert-pdf", response_model=PdfConversionResponse)
async def convert_pdf(
    request_body: PdfConversionRequest,
    request: Request,
    user: dict = Depends(require_auth)
):
    """Convert the first page of a PDF to an image.
    
    SECURITY: Requires authentication and rate limiting.
    V5: Added auth + rate limiting for security
    """
    # Rate limiting
    await require_plus_access(db, user)
    await check_rate_limit(request, "convert_pdf", user)
    
    logger.info(f"[CONVERT PDF] User {user.get('email', 'unknown')} requesting PDF conversion")
    
    try:
        if not request_body.pdf_base64:
            raise HTTPException(status_code=400, detail="No PDF data provided")
        
        # Log input stats for debugging
        input_length = len(request_body.pdf_base64)
        logger.info(f"convert-pdf endpoint called: base64_length={input_length}")

=== SERVICE TRIAL RECORD LOOKUP ===
    if not isinstance(value, datetime):
        return None

    if value.tzinfo is None:
        return value.replace(tzinfo=timezone.utc)

    return value.astimezone(timezone.utc)


async def _get_trial_access(
    db: Any,
    user_id: str,
) -> Dict[str, Any]:
    record = await db.subscription_access.find_one(
        {
            "$or": [
                {"_id": user_id},
                {"user_id": user_id},
            ]
        }
    )

    if not record:
        now = _utc_now()

        await db.subscription_access.update_one(
            {"user_id": user_id},
            {
                "$setOnInsert": {
                    "user_id": user_id,
                    "trial_started_at": now,
                    "trial_ends_at": now + timedelta(days=30),
                    "created_at": now,
                },
                "$set": {
                    "updated_at": now,
                },
            },
            upsert=True,
        )

        record = await db.subscription_access.find_one(
            {"user_id": user_id}
        )

        if not record:
            raise HTTPException(
                status_code=503,
                detail="Subscription access is temporarily unavailable.",
            )

    trial_started_at = _normalize_datetime(
        record.get("trial_started_at")
    )
    trial_ends_at = _normalize_datetime(
        record.get("trial_ends_at")
    )

    if not trial_ends_at:
        return {
            "active": False,
